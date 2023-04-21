# Reno Congestion Control

Classic Reno CC algorithm fits in one page from `linux/net/ipv4/tcp_cong.c`

```
/*
 * TCP Reno congestion control
 * This is special case used for fallback as well.
 */
/* This is Jacobson's slow start and congestion avoidance.
 * SIGCOMM '88, p. 328.
 */
void tcp_reno_cong_avoid(struct sock *sk, u32 ack, u32 acked)
{
        struct tcp_sock *tp = tcp_sk(sk);

        if (!tcp_is_cwnd_limited(sk))
                return;

        /* In "safe" area, increase. */
        if (tcp_in_slow_start(tp)) {
                acked = tcp_slow_start(tp, acked);
                if (!acked)
                        return;
        }
        /* In dangerous area, increase slowly. */
        tcp_cong_avoid_ai(tp, tcp_snd_cwnd(tp), acked);
}
EXPORT_SYMBOL_GPL(tcp_reno_cong_avoid);

/* Slow start is used when congestion window is no greater than the slow start
 * threshold. We base on RFC2581 and also handle stretch ACKs properly.
 * We do not implement RFC3465 Appropriate Byte Counting (ABC) per se but
 * something better;) a packet is only considered (s)acked in its entirety to
 * defend the ACK attacks described in the RFC. Slow start processes a stretch
 * ACK of degree N as if N acks of degree 1 are received back to back except
 * ABC caps N to 2. Slow start exits when cwnd grows over ssthresh and
 * returns the leftover acks to adjust cwnd in congestion avoidance mode.
 */
u32 tcp_slow_start(struct tcp_sock *tp, u32 acked)
{
        u32 cwnd = min(tcp_snd_cwnd(tp) + acked, tp->snd_ssthresh);

        acked -= cwnd - tcp_snd_cwnd(tp);
        tcp_snd_cwnd_set(tp, min(cwnd, tp->snd_cwnd_clamp));

        return acked;
}
EXPORT_SYMBOL_GPL(tcp_slow_start);

/* In theory this is tp->snd_cwnd += 1 / tp->snd_cwnd (or alternative w),
 * for every packet that was ACKed.
 */
void tcp_cong_avoid_ai(struct tcp_sock *tp, u32 w, u32 acked)
{
        // I guess "ai" here stands for additive increase
        // ...
}

/* Slow start threshold is half the congestion window (min 2) */
u32 tcp_reno_ssthresh(struct sock *sk)
{
        const struct tcp_sock *tp = tcp_sk(sk);

        return max(tcp_snd_cwnd(tp) >> 1U, 2U);
}
EXPORT_SYMBOL_GPL(tcp_reno_ssthresh);
```

Where `tcp_in_slow_start()` and `tcp_is_cwnd_limited()` are defined in `linux/include/net/tcp.h':


```
static inline bool tcp_in_slow_start(const struct tcp_sock *tp)
{
        return tcp_snd_cwnd(tp) < tp->snd_ssthresh;
}

/* We follow the spirit of RFC2861 to validate cwnd but implement a more
 * flexible approach. The RFC suggests cwnd should not be raised unless
 * it was fully used previously. And that's exactly what we do in
 * congestion avoidance mode. But in slow start we allow cwnd to grow
 * as long as the application has used half the cwnd.
 * Example :
 *    cwnd is 10 (IW10), but application sends 9 frames.
 *    We allow cwnd to reach 18 when all frames are ACKed.
 * This check is safe because it's as aggressive as slow start which already
 * risks 100% overshoot. The advantage is that we discourage application to
 * either send more filler packets or data to artificially blow up the cwnd
 * usage, and allow application-limited process to probe bw more aggressively.
 */
static inline bool tcp_is_cwnd_limited(const struct sock *sk)
{
        const struct tcp_sock *tp = tcp_sk(sk);

        if (tp->is_cwnd_limited)
                return true;

        /* If in slow start, ensure cwnd grows to twice what was ACKed. */
        if (tcp_in_slow_start(tp))
                return tcp_snd_cwnd(tp) < 2 * tp->max_packets_out;

        return false;
}
```

`tcp_reno_cong_avoid()` gets called from `tcp_rcv_established()`.

```
tcp_rcv_established()
  -> tcp_queue_rcv()  // If income segment have payload
  -> tcp_ack()  // This routine deals with incoming acks, but not outgoing ones.
    -> tcp_cong_control() // Common entry point, called toward the end of
                          // processing an ACK with precise rate info.
                          // All transmission or retransmission are delayed afterwards.
      -> if tcp_in_cwnd_reduction():
           tcp_cwnd_reduction()
         elif tcp_may_raise_cwnd():
            tcp_cong_avoid(): icsk->icsk_ca_ops->cong_avoid()
              -> tcp_reno_cong_avoid() or cubictcp_cong_avoid()
  -> tcp_data_snd_check()   // If we can send more data
    -> tcp_push_pending_frames()
    -> tcp_check_space()  // check if new space made available
      -> tcp_new_space()  // if SOCK_NOSPACE is set in sk->sk_socket->flags
        -> if tcp_should_expand_sndbuf():
             tcp_sndbuf_expand()
  -> __tcp_ack_snd_check()  // Check if sending an ack is needed.
```
