1. failure, Byzantine failure与fail-stop processor

   From Andrea C. Arpaci-Dusseau [cs739 lecture slide](http://pages.cs.wisc.edu/~dusseau/Classes/CS739-S06/Writeups/failstop.pdf)


    What is a failure?
    - Output (or behavior) that is inconsistent with specification


    What is a Byzantine failure?
    - Arbitrary, even malicious, behavior
    - Components may collude with each other
    - Cannot necessarily detect output is faulty


    What is a fail-stop processor?
    - Halts instead of performing erroneous transformations
    - Others can detect halted state
    - Other can obtain uncorrupted stable storage

2. 