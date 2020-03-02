# Notes - Data Transmission and Control Section

#### TSBPD Time Base Calculation {#tsbpd-time-base}

1. Some introductory notes - not necessary to add

<!-- Timestamps are relative to the connection. The transmission is not based on an absolute time.
The scheduled execution time is based on a real clock time. A time base is maintained to
convert each packet‘s timestamp to local clock time. Packets are offset from the sender‘s
StartTime. Any time reference based on local StartTime is maintained, taking into account RTT,
time zone and drift caused by the sum of truncated nanoseconds and other measurements. -->

2. TSBPD wrapping period: add pseudo-code.

## Too-Late Packet Drop {#too-late-packet-drop}

1. Rephrase this part
<!-- Implementation related, delete implementation details in future -->
In the SRT sender, when Too-Late Packet Drop is enabled, a packet is
considered too late to be delivered and may be dropped by the sending
application if its timestamp is older than 125% of the SRT latency.
However, the sender keeps packets for at least 1 second in case the
SRT latency is not enough for a large RTT (that is, if 125% of the
SRT latency is less than 1 second).

2. Pictures and tables to illustrate pseudo-code

This table should be corrected, but as an example

| s @ Dst | SrcTime (PKT_TIMESTAMP) | SND Clock   | Time Base    | RCV Clock   | SRT Latency | Drift | Packet Delivery Time |   |
|---------|-------------------------|--------------|--------------|--------------|-------------|-------|----------------------|---|
| 1       | 20                      | 00:00:00,020 | 00:00:00,040 | 00:00:00,060 | 120         | 0     | 00:00:00,180         |   |
| 2       | 40                      | 00:00:00,040 | 00:00:00,040 | 00:00:00,080 | 120         | 0     | 00:00:00,200         |   |
| 5       | 100                     | 00:00:00,100 | 00:00:00,040 | 00:00:00,140 | 120         | 0     | 00:00:00,260         |   |
| 6       | 120                     | 00:00:00,120 | 00:00:00,040 | 00:00:00,160 | 120         | 0     | 00:00:00,280         |   |
| 7       | 140                     | 00:00:00,140 | 00:00:00,040 | 00:00:00,180 | 120         | 0     | 00:00:00,300         |   |
| 8       | 160                     | 00:00:00,160 | 00:00:00,040 | 00:00:00,200 | 120         | 0     | 00:00:00,320         |   |
| 3       | 60                      | 00:00:00,060 | 00:00:00,040 | 00:00:00,210 | 120         | 0     | 00:00:00,220         |   |
| 4       | 80                      | 00:00:00,080 | 00:00:00,040 | 00:00:00,212 | 120         | 0     | 00:00:00,240         |   |
| 9       | 180                     | 00:00:00,180 | 00:00:00,040 | 00:00:00,220 | 120         | 0     | 00:00:00,340         |   |
| 10      | 200                     | 00:00:00,200 | 00:00:00,040 | 00:00:00,240 | 120         | 0     | 00:00:00,360         |   |

3. According to Maxim, this additional delay is implementation related

From API

NB: The default live mode settings set SRTO_SNDDROPDELAY to 0. The buffer mode settings set SRTO_SNDDROPDELAY to -1.

[SET] - Sets an extra delay before TLPKTDROP is triggered on the data sender. TLPKTDROP discards packets reported as lost if it is already too late to send them (the receiver would discard them even if received). The total delay before TLPKTDROP is triggered consists of the LATENCY (SRTO_PEERLATENCY), plus SRTO_SNDDROPDELAY, plus 2 * the ACK interval (default ACK interval is 10ms). The minimum total delay is 1 second. A value of -1 discards packet drop. SRTO_SNDDROPDELAY extends the tolerance for retransmitting packets at the expense of more likely retransmitting them uselessly. To be effective, it must have a value greater than 1000 - SRTO_PEERLATENCY.