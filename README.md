# spine-using-IPv4-GRE
 Spine idea from Jennifer Rexford's lab modified to use IPv4 GRE

Spine idea from Jennifer Rexford's lab modified to use IPv4 GRE

Internet Protocol (IP) addresses can reveal information about communicating Internet users and devices, even when the rest of the traffic between them is encrypted. At the same time, IP addresses serve as endpoints for network-layer communication and, as a result, are typically visible to the intermediate routers to allow them to forward traffic to its ultimate destination. Previous approaches to obfuscate the IP addresses of the sender and receiver commonly depend on either custom user software (e.g., Tor browser) or significant modifications to network hardware along the end-to-end path (which has proved to be a major roadblock). SPINE, on the other hand, conceals IP addresses and relevant TCP fields from intermediate—and potentially adversarial—autonomous systems (ASes) but requires only two participating ASes and no cooperation from end hosts. To demonstrate SPINE’s practicality, we have implemented it on commodity programmable switches using the P4 programming language. Our evaluation shows that SPINE can run at hardware rates on commodity switches, paving the way to real-world deployment.

Disadvantages of SPINE :

Depends on IPv6 based forwarding when not all Autonmous Systems in the internet have v6 deployed. A new scheme that uses GRE IPv4-in-IPv4 encapsulation can be accomplished.

Says IPSEC encrypts the whole payload whereas a new version of this scheme can be adopted where the IPSEC keys are exchanged for encrypting the header alone. In the case of IP-SEC the v6 encapsulation need not be done and is obviated completely. Normal IP-SEC encapsulation with header encryption alone can be accomplished.
Things in the new scheme that remain constant from SPINE :

SDN controllers can be used to send the keys and version numbers.
The GRE key and sequence number fields can be used to carry the version # and nonce as in the spine idea. Also the E(ip) can be carried in the key and sequence number fields.

Advantage in the new scheme is that the size of the outer encapsulation header is lesser with v4 than with a v6 outer header.

Both the source and destination v4 header can be encrypted and carried in the GRE header.
A specific v4 prefix can be used between the trusted entity ASes to identify that a new-SPINE packet is being received and needs decryption at one of its entry points or at a suitable location within the receiving trusted entity AS.

Encryption function , PRF hash function, the XOR operation and the key rotation through SDN controller can be kept as is in SPINE.

With IP-SEC header alone encryption option we can arrive at no SDN controller at all since IPSEC key configuration is done without the help of a central SDN controller. Currently both tunnel and transport modes of IPSEC encrypt the whole packet. This can be modified a bit and a new IPSEC scheme that encrypts the IP and TCP headers of the original packet alone can be provided. Need to investigate how this can be done or suggested in the new-spine scheme. With GRE this can be taken care of easily, since the outer IP header is NOT the same as the inner header. In IPSEC tunnel mode the outer header is different than the inner. In IPSEC Transport mode the outer IP (src and dest) is the same except for modifications to the protocol fields. This behaviour can be modified to propose a scheme wherein in transport mode the outer will be that of the new-spine entity. The new spine trusted entity (sending AS's) router will recognize a IP-SEC transport mode packet and replace the outer header with its own with a prefix dr to be used (in v4 space) for the new-spine scheme. The receiving trusted entity's AS router will recreate the outer IPSEC header and fix it before it sends it to the end host. If the modifications to the outer header are predictable by IPSEC transport mode then the recreation should be straight forward at the receiving trusted entity's AS router.

Also the new scheme could create an architecture for proper and authenticated discovery of trusted AS entitys that perform this enc and dec functions. Their specific advertisements could be authenticated and their AS path through the internet could be verified before arriving at an agreement as to whether the trusted entity (so advertising) can be used for enc and dec of IP headers of flows between the subscribers of these services. The end enterprises could discover the encrypting entity AS which in turn could pair up with other dec entity ASes. Both the encrypting and decrypting entity ASes information could be passed to the enterprises or end hosts using this service and they could choose an appropriate pair of entity ASes to perform this scheme.

This mechanism can be end-to-end also and through trusted entities being the BORDER router / firewall and or NAT between the communicating end hosts. We can subvert DOS attacks on end hosts in an enterprise network by requiring a certain packet format - either IPSEC or GRE based schemes and filter packets from spoofing entities automatically at the border box.

Using TCP options and TCP option data this scheme can authenticate bad / spoofed packets from good ones. The TCP data should contain an identifier that identifies the source as authentic and should be traceable back to the source.

IPSEC header alone encryption we should propose a new draft. Payload need not be double encrypted when using IPSEC schemes.
inter DC communication can use this new SPINE scheme for preventing snooping of headers and payload by intermediate non trusted entities.

BGP internet AS graph can be constructed and the AS path from new-SPINE advertising trusted enc and dec entities and their AS paths can be compared with the AS topology to find how many untrusted entities lie between the two (sender/enc and recvr/dec) and choose to deploy or not deploy the SPINE scheme.
inter AS TE path between the trusted enc and dec.

DC environments can deploy enc and dec of TCP/UDP headers using SPINE based enc and dec schemes based on which neighboring VMs cannot snoop into the communication.
