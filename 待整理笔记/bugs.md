1. Using Visual Studio:   
same code, run perfect in my laptop  
seg fault in friend's laptop  
reason: didn't clean and rebuild. array seg fault.  

2. Memory not enough and app dead at 7am(market data dept.)
Binary search and find a special msg comes in every day at 7am  
However that's a normal msg. less than 1kb.
Reason: that msg causes reloading a huge file. (GW). And we were using a memory limit (ulimit) util.
Solve: Increase ulimit  

3. Add a comment, dead  
Reason: some pointer floating around.  

4. OTC msg wrong  
an OTC msg comes in, should not update normal ticker  
However no logic error in code  
Reason: the exchange sends a normal msg with same fields together with this OTC msg.
