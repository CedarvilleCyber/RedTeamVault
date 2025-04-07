Here is a collection of best practices for Red Team OPSEC during engagements. 

1. Limit egress traffic
Egress traffic is the C2 traffic flowing out of the defender's network. This traffic is highly sensitive, and is vulnerable to discovery upon close inspection. By utilizing chains of implants in pivot-listener or P2P networking relationships, we can limit our egressing hosts to one or two, and therefore limit the number of points at which our traffic crosses the critical barrier between the defending network and the WAN. 
2. Longer sleeps are better
The number of times a beacon checks in during a given time period is directly proportional to its likelihood of detection and interception. Long sleeps between checkins is good for OPSEC, as NIDS have a limited window of packets they can store in memory for correlation and heuristic analysis. 
3. Be gentle during initial access
Account lockout policies need to be balanced with time on engagement. This is the trickiest part to keep quiet, especially while enumerating users or spraying passwords, but it is necessary. 

# Cobalt Strike
1. The default DNS record for CS is well signatured and will cause problems with NIDS. Be sure to modify it. 
2. The default SMB named pipe is well signatured. This will need to be changed in your Malleable C2 config. 
3. 