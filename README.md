# Reliable Transport Protocol

## Overview

This project implements a basic reliable transport protocol using UDP with a focus on handling packet loss, out-of-order delivery, congestion and other network issues. Since UDP does not guarantee packet delivery, order, or integrity, additional functions are designed for reliability, including acknowledgments, checksums, sequence tracking, and congestion control. The protocol consists of two main programs:

- **Sender**: Sends data packets to the receiver, tracks ACKs, and handles retransmissions in case of packet loss or errors.
- **Receiver**: Listens for incoming data, validates packet integrity, and sends ACKs.


## Features

1. **Environment Setup**:
   - Sets up socket communication between sender and receiver.
   - The `Sender` establishes a connection to the receiver and sends data packets.
   - The `Receiver` binds to a specific port and waits for incoming packets.

2. **Packet Structure**:
   - Each packet has a **sequence number** to track the order and a **checksum** to verify integrity.
   - The `Sender` generates a checksum for each packet to detect errors during transmission.
   - The `Receiver` verifies each packet's checksum and sequence number before acknowledging.

3. **Reliability & Congestion Control**:
   - The `Sender` uses a **window-based** approach to control the number of in-flight packets.
   - **Congestion control** mechanisms are applied, adjusting the window size dynamically based on acknowledgment feedback and network conditions.
   - Retransmissions are triggered on timeout or when three duplicate acknowledgments are received.

4. **Loop in Sender Class**:

   - The loop continues sending packets and processing ACKs until all data has been sent and acknowledged.
   - It reads from the data input and creates data packets to send if they fit within the sliding window. Each packet is assigned a sequence number and is sent via the send method.

5. **Loop in Receiver Class**:
   - The loop uses select.select to check if data is available on the socket for reading. 
   - The recv method is called to read the data packet from the socket.
   - After processing each packet, the receiver sends an ACK to the sender.


## Challenges Faced #

* To reduce overhead and improve performance, especially in test 8, we focused on eliminating JSON encoding and directly transmitting byte data. Using packets instead of JSON reduces parsing time and decreases the overall packet size, which optimizes data throughput.

* One of the main challenges in implementing reliable data transmission was efficiently managing timeouts and congestion control without overwhelming the network. It was solved by dynamically adjusting the congestion window and selectively retransmitting only unacknowledged packets, optimizing for both network efficiency and reliability.


## Testing Strategy #

* We ran the UDP transport protocol on all configs provided in the assignment. Each configuration was tested to verify that our protocol handled each scenario correctly, including cases with packet corruption, packet drops, packet reordering, and duplicate packets, among others.

* To gain better visibility into the protocol's operations, we added print statements after critical events, such as receiving and sending messages. This allowed us to monitor the sequence of actions and identify any issues quickly.