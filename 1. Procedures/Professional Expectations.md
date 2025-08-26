# Accountability

Our actions should be able to be validated by logs from our local penetration testing machines. Additionally, we should be documenting our actions on compromised hosts throuroughly and understanding our exploits before we run them. The trust placed in a penetration tester to not destroy the domain can be shaky, and we need to do our best to maintain it. 

This is an easy solution for bash shell logging and timestamps for attacks run for deconfliction and plausible deniability in the event of a mishap: https://unix.stackexchange.com/questions/25639/how-to-automatically-record-all-your-terminal-sessions-with-script-utility

# Scope

Our actions should be kept within the scope at all times. At no point should malicious traffic be pointed outside of scope. Clarify with your customer if that includes scans, only exploitations, lateral movements, etc. Know your scope well. Likely, out-of-scope targets should not be touched at all, and can be excluded from all hosts by whatever means necessary. Learn CIDR and use it if you must. 

# Integrity of Data

We need to protect the data of the customer. Sometimes we will be provided accounts with which to do this, meaning we should not be retaining data from our customer on our own drives. 

HOWEVER

We MUST keep a copy of the penetration report ourselves for defensibility. We will maintain this copy for a period not less than 1 year before disposal. 

We MUST keep copies of our actions on target, redacted as necessary to preserve customer data. We must maintain our accountability and defensibility from our customer as well as for them. 
