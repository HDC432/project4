# UDP Reliable Data Transmission System

This project implements a basic reliable data transmission system using UDP with a focus on handling packet loss, out-of-order delivery, and congestion. The system consists of two main programs:

- **Receiver**: Listens for incoming data, validates packet integrity, and sends ACKs.
- **Sender**: Sends data packets to the receiver, tracks ACKs, and handles retransmissions in case of packet loss or errors.

## Overview

The program uses UDP, so it must handle reliability, retransmission, and congestion control independently. The goal is to ensure that all packets are received in the right order.

## Features

1. **Environment Setup**:
   - Sets up socket communication between sender and receiver.
   - The `Receiver` binds to a specific port and waits for incoming packets.
   - The `Sender` establishes a connection to the receiver and sends data packets.

2. **Packet Structure**:
   - Each packet has a **sequence number** to track the order and a **checksum** to verify integrity.
   - The `Sender` generates a checksum for each packet to detect errors during transmission.
   - The `Receiver` verifies each packet's checksum and sequence number before acknowledging.

3. **Reliability & Congestion Control**:
   - The `Sender` uses a **window-based** approach to control the number of in-flight packets.
   - **Congestion control** mechanisms are applied, adjusting the window size dynamically based on acknowledgment feedback and network conditions.
   - Retransmissions are triggered on timeout or when three duplicate acknowledgments are received.


## How It Works

### Receiver Program

1. **Initialize and Bind Socket**: The receiver sets up a UDP socket and binds it to a port to listen for incoming data.
2. **Receive Packets**: For each packet, the receiver verifies the checksum, validates the sequence number, and stores any out-of-order packets in a buffer.
3. **Send Acknowledgments**: The receiver sends an ACK for each packet received, confirming the last valid sequence number.
4. **Buffer and Reorder Data**: Out-of-order packets are buffered and reordered, ensuring the correct sequence is printed to the console.

### Sender Program

1. **Send Packets with Checksum and Sequence Number**: Each packet includes a checksum and sequence number for validation.
2. **Congestion Control and Retransmission**: The sender tracks the round-trip time (RTT) and adjusts the timeout for each packet based on ACK feedback. Duplicate ACKs or timeouts trigger retransmission.
3. **Adapt Window Size**: Uses congestion control techniques like slow-start and congestion avoidance to adapt to network conditions.
4. **Track Unacknowledged Packets**: The sender keeps a record of sent packets and retransmits if needed.


# Challenges Faced #

* To reduce overhead and improve performance, especially in test 8, we focused on eliminating JSON encoding and directly transmitting byte data. Using packets instead of JSON reduces parsing time and decreases the overall packet size, which optimizes data throughput.

* One of the main challenges in implementing reliable data transmission was efficiently managing timeouts and congestion control without overwhelming the network. It was solved by dynamically adjusting the congestion window and selectively retransmitting only unacknowledged packets, optimizing for both network efficiency and reliability.


# Testing Strategy #

We ran the UDP transport protocol on all configs provided in the assignment. Each configuration was tested to verify that our protocol handled each scenario correctly, including cases with packet corruption, packet drops, packet reordering, and duplicate packets, among others.

To gain better visibility into the protocol's operations, we added print statements after critical events, such as receiving and sending messages. This allowed us to monitor the sequence of actions and identify any issues quickly.