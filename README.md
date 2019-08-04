# OPTIMIZING-ARP-HANDLING-IN-SOFTWARE-DEFINED-NETWORK-

Environment Setting:
      -> Install mininet with pox controller on ubuntu 16.04 LTS.
          -> apt-get install mininet
          -> mn -c
          -> git clone git://github.com/mininet/mininet  
          -> cd mininet
          -> git tag
          -> git chechout -b <final version>
          -> ./util/install.sh -a
          -> run "sudo mn" to check if mininet is sucessfully installed
          -> For further clarification refer https://youtu.be/UXBHUoRHVas   
      -> Install gnuplot
         -> apt-get install gnuplot
      

How to run the code and interpret the results:
We are comparing the network performance in terms of throughput and delay with and without the implemented solution.

  Case1: Without the implemented solution
       -> Start the pox controller inside the pox directory using the following command: "./pox.py"
       -> In a new terminal run the following command to create the topology "sudo mn --mac  --custom topology.py --topo topology"
       -> Inside the mininet choose any three hosts and run "xterm <host>" to get the host terminal
       -> Inside the host terminals run "arp -a" and confirm that there are no arp caches
       -> Inside the host terminals run "sudo wireshark"
       -> Select the appropriate interface to capture the packets in wireshark
       -> Inside the mininet run "xterm c0" to open the controller terminal
       -> Inside the controller terminal run "arp -a"
       -> Inside the controller terminal run "sudo wireshark"
       -> Select the "any" interface to capture the packets in wireshark
       -> Inside the mininet run "<host1> ping <host2>"
       -> We observe that we receive ARP broadcast packets on all the interfaces.
	-> Inside the mininet run "<host3> ping <host2>"
	-> We still observe that ARP Request packets for host2 are broadcasted.
       -> Close all the host terminals
       -> exit from mininet using "quit" 


       -> Recreate the mininet topology 
       -> Inside the mininet choose any two hosts and run "xterm <host>" to get the host terminal
       -> Inside the host terminal run "arp -a" to confirm that arp cache is empty
       -> Run this host as an iperf server using the command "iperf -s -p <arbitary port> -i 1 > <filename>" // this host acts as a server listening on arbitary port for every 1 milli second and the result is saved in the file
       -> In the other multiple host terminals generate traffic using the following command "iperf -c <server host ip> -p <server port> -t 15" //This host acts as a client generating traffic to the server with ip"server ip" listening on port"server port" for 15 seconds
       -> Wait for the command prompt on the client host
       -> Press ctrl c in the server host terminal to stop listening
       -> View the file containing the result using the command "more <filename>"
       -> In order to plot a graph between interval and bandwidth use the following command to get the set of interval and bandwidth values from the result file "cat <filename> | grep sec | head -15 | tr - " " | awk '{print $4,$8}' > <new result file>" 
       -> Run "gnuplot"   
       -> Inside gnuplot run "plot "<new result file>" title "<title of the graph>" with linespoints"
       -> A graph pops up on the screen



Case2: With our implemented solution
       -> Copy arp_responder.py and l2_learning_arp_handling.py scripts into pox/ext directory.
       -> Start the pox controller inside the pox directory using the following command: "./pox.py log.level --DEBUG proto.dhcpd --network=10.1.1.0/24 --ip=10.1.1.1 l2_learning_arp_handling arp_responder"
	-> In a new terminal run the following command to create the topology "sudo mn --mac  --controller remote --custom topology.py --topo topology"
       -> Repeat all the steps from case 1 until "<host3> ping <host2>"
	-> We observe that the ARP Request for host2 is not broadcasted. 
	-> Continue the next steps as above.
	-> Check the graph generated with this solution.



Now compare the graphs obtained in both the cases to observe throughput
