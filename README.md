# File-Transfer-using-UDP-and-TCP-Protocol
TCP

TCP have three methods: send, receive and timeout. The TCP uses Sockets. Thus any of our machines can call the TCP class and use its methods to be the sender or receiver. For the purposes of our demonstration, the class has a hardcoded packetsize of 1000 bytes and a hardcoded timeout of 0.5 seconds.

Send w/ Timeout and Retransmission
In the send method the program first generates a checksum using our ChecksumGen class methods and stores the checksum to be inserted into the header object with each packet. Then it starts a timer. As long as the end of the file isn’t reached, the file is broken up into at most 1000 byte pieces and inserted into our header object. We also generate a random non-zero sequence number and every packet receives a calculated increment of that sequence number.
After filling our header with the checksum, payload and sequence number we send it using the ObjectStream through our TCP Socket.
In order to facilitate retransmission, we also retain the packets we sent in a buffer. Whenever there is a timeout, the send method calls the timeout method.
Within the timeout method we check if the ACKs we’ve been receiving are repeating or not. If they are repeating, we search the buffer for the packet with that sequence number and restransmit it. If the ackknowledgements are not repeating, we exit back to the send method.
Once all the packets have been sent, we wait for confirmation from the receiver on whether the checksum matched or not and then we exit the program.
Receive
In the receive method the program first creates a buffer. As long as the machine is receiving non-null packets of data it continues being in the receive mode. To demonstrate our timeout and retransmission, we deliberately drop the third packet. If the timeout code is commented out then the final checksum will show a mismatch.
Whenever it receives a header, we check if the sequence number of that packet is the one we just sent an ACK for. If so, we add that header object into the buffer. If it is not a matching sequence number, that indicates a dropped packet and the code prevents the update of our ack until the dropped packet is retransmitted.
However, before the dropped packet is retransmitted, if we receive other packets, we add those to our buffer. Once the packet has been retransmitted, we update our ACK to the latest packet’s and resume sending ACKs as usual.
Once we have received all the packets we generate a checksum for the received file. We then compare the generated checksum with the one that was sent in the headers with the packets. If the checksum matches or doesn’t match, we message the sender letting them know and exit the program.

UDP
It created a constructor and two methods. The UDP uses Datagram Sockets and Datagram Packets. For the purposes of our demonstration, the class has a hardcoded packetsize of 1000 bytes and a hardcoded wait-time of 10 seconds.

Send
The method first generates a checksum of the file to be sent and adds an indentifiable sentinel to the checksum string. Then it converts the string to a byte array to be send via UDP as a DatagramPacket object.
After that, the file is broken into pieces of at most 1000 bytes and sent as DatagramPackets until the entire file is sent. Since UDP does not gaurantee transmission, the sender does not wait for a response on whether the checksum matched or not.

Receive
The method keeps listening for DatagramPackets being sent to it, and whenever it receives one it decapsulates the packet to get the payload. For each packet, it checks for the checksum identifier sentinal. If the packet is a checksum, it stores the value, otherwise it writes the payload onto a file.
If at any point, the method hasnt received a new packet for the last 10 seconds, it begins to check the validity of the received file. It generates a new checksum for the file it wrote fromt he received packets and prints a statement to the user if the new generated checksum matches the one it was sent by the sender.

header
header class is a truncated version of the actualy protocol headers because most of the extra fields are taken care of and utilized by the Socket libraries.
Our header consists of a byte array payload, a long integer sequence number and a checksum String.
There are two methods in the class, a constructor and a comparator to enable sorting of the buffer.

ChecksumGen
ChecksumGen class takes care of generating checksums for the sent and received files. It implements a pre-existing md5 algorithm that we did not create and uses the MessageDigest class from java’s security library.

Contributor: Mohammed Mustafa and Elham Hamid
