For an enterprise network, we have different ways to physically connect to our Datacenter to AWS Datacenter 

For a private network of a Datacenter (Basically bunch of lan cables going to switches, everything on same network but if we want to connect to outside world, we need to attach these switches to a router)

1. For a lesser workload and if we want to connect to AWS, would be a way to create a VPN on AWS & connect to AWS VPN to your private network. Going through VPN would be going through internet, using shared resources on internet. 

2. For a bigger Giants where they want low latency and higher speed connections, they can connect to the nearest AWS datacenter and AWS provides "private connect". Basically a cable (10 Gbps) and it will be connecting to a nearest AWS location.

3. In case of any geographical difference,  it cane be connected through a third party vendors authorized by AWS as Vendors depending on your locations. 

Screenshots/image-50.png