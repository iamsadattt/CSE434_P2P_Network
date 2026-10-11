# UAP P2P Network — Peer Communication and File Sharing

**Course:** CSE 433 – Blockchain & Distributed Security Lab  
**University:** University of Asia Pacific  
**Department:** Computer Science and Engineering  
**Student Name:** Md. Sadat Samin  
**Student ID:** 22201006  
**Section:** AB

---

## GitHub Repository

**Repository Link: ** [Add your GitHub repository URL here]

---

## 1. Introduction

The **UAP P2P Network** is a Python-based peer-to-peer application developed with TCP sockets. It allows multiple computers or program instances to communicate and exchange files directly without depending on a central server.

Each instance of the application operates as an independent peer and performs two functions simultaneously:

- **Server:** Opens a listening port to accept connections from other peers.
- **Client:** Establishes connections to remote peers using their IP addresses and port numbers.

After a connection has been established, peers can communicate using text messages or transfer files of different formats, including images, audio, video, PDF documents, ZIP archives, and ordinary text files. Files are transferred as binary data, so the application does not need to understand their individual formats.

The application supports connections with multiple peers at the same time. A Tkinter-based graphical user interface (GUI) provides a list of connected peers, controls for sending messages and files, and a log for monitoring communication and network events.

### Main Features

| Feature | Implementation |
|---|---|
| Peer initialization | The user provides a peer name and listening port before the server socket starts. |
| Direct connection | A peer connects to another peer through its IP address and port. |
| Peer identification | A `hello` and `hello_ack` handshake exchanges `peer_id`, `peer_name`, and `port`. |
| Concurrent connections | Separate threads handle individual connections, allowing multiple peers to communicate concurrently. |
| Message framing | Each JSON message is preceded by a 4-byte length header. |
| Text communication | Messages use the `text` protocol type and are sent to the currently selected peer. |
| File sharing | File metadata is sent first, followed by the file's raw bytes in 64 KB chunks. |
| Download management | Received files are stored in the `downloads/` directory without overwriting existing files. |
| Error management | Problems such as invalid ports, connection failures, missing files, and unexpected disconnections appear in the GUI log. |

---

## 2. Software and System Requirements

The following software and hardware environment is sufficient to run the application.

- **Python:** Version 3.9 or newer.
- **GUI library:** Tkinter.
- **Supported operating systems:** Windows, Linux, and macOS.
- **Network:** A local computer connection for testing or a shared Wi-Fi/LAN network for communication between computers.
- **External dependencies:** None. The application uses modules from Python's standard library.

The standard library modules used by the project include:

`socket`, `threading`, `json`, `struct`, `os`, `queue`, and `tkinter`.

Tkinter is normally available with Python installations from python.org on Windows and macOS. On Ubuntu or Debian-based Linux systems, it may need to be installed separately:

```bash
sudo apt install python3-tk
```

No additional packages need to be installed through `pip`.

---

## 3. Directory and File Organization

The source code is arranged into separate files so the graphical interface, networking operations, and message protocol remain organized.

```text
P2P_Network/
|
|-- main.py             # GUI, input controls, and user actions
|-- p2p_node.py         # Peer networking, sockets, threads, and file transfer
|-- protocol.py         # Message framing and protocol message creation
|-- requirements.txt    # Dependency information
|-- README.md           # Project documentation
|
|-- downloads/          # Destination for files received from other peers
|
|-- screenshots/        # Screenshots used in the project report
```

The three main Python files have different responsibilities:

- **`main.py`:** Creates the Tkinter window and manages user interaction.
- **`p2p_node.py`:** Handles incoming and outgoing connections, peer handshakes, text communication, and file exchange.
- **`protocol.py`:** Defines the message format, length-prefix framing, and builders for the `hello`, `hello_ack`, `text`, and `file` messages.

The `requirements.txt` file is included for project organization, although no third-party package installation is necessary.

---

## 4. Installation and Initial Configuration

Follow these steps to prepare the application.

**Step 1: Install Python**

Download Python 3.9 or a newer release from the official website:

https://www.python.org/downloads/

During installation on Windows, enable **Add Python to PATH**.

**Step 2: Verify the installation**

Open Command Prompt or a terminal and execute:

```bash
python --version
```

On some Linux or macOS systems, use:

```bash
python3 --version
```

Confirm that the installed version is Python 3.9 or above.

**Step 3: Extract the project**

Extract the submitted ZIP archive. Ensure that the `P2P_Network` directory contains `main.py`, `p2p_node.py`, and `protocol.py`.

**Step 4: Check the dependencies**

There is no need to run `pip install` because the project relies on Python's built-in libraries.

**Step 5: Install Tkinter if necessary**

For Ubuntu or Debian systems where Tkinter is unavailable, run:

```bash
sudo apt install python3-tk
```

After completing these steps, the project is ready to launch.

---

## 5. Starting a Peer

Open a terminal in the `P2P_Network` directory and execute:

```bash
python main.py
```

A window titled **UAP P2P Network** will appear.

To run several peers on the same computer, launch `main.py` from separate terminal windows. Each running instance must use a different listening port.

For every instance:

1. Enter a peer name, such as `Alice`.
2. Enter a listening port, such as `5000`.
3. Select **Start Peer**.

Once startup is successful, the interface displays the peer's details. For example:

```text
Alice | ID: a83f21c4 | Port: 5000
```

The event log also records a message similar to:

```text
[SYSTEM] Peer started: Alice [a83f21c4] on port 5000
```

This confirms that the peer has started listening for incoming connections.

**Screenshot:** `screenshots/01_start_peer.png`

![Peer startup example](screenshots/01_start_peer.png)

---

## 6. Establishing Connections Between Peers

### A. Running Two Peers on One Computer

For this example, use the following configuration:

| Peer | Display Name | Listening Port |
|---|---|---:|
| Peer A | Alice | 5000 |
| Peer B | Bob | 5001 |

Follow these instructions:

1. Open two terminal windows.
2. Launch Alice on port `5000` and Bob on port `5001`.
3. In Bob's interface, locate the **Connect to Another Peer** section.
4. Enter `127.0.0.1` as the IP address and `5000` as the remote port.
5. Click **Connect**.

The loopback address `127.0.0.1` refers to the same computer. Bob can therefore reach Alice through this address without requiring a second computer.

After a successful connection, both interfaces should list the other peer. Their event logs should contain connection messages similar to:

```text
[SYSTEM] Connected to Alice
```

Alice's log should likewise indicate that Bob has connected.

**Screenshots:**

- `screenshots/02_peerA_connected.png`
- `screenshots/03_peerB_connected.png`

![Peer A after connecting](screenshots/02_peerA_connected.png)

![Peer B after connecting](screenshots/03_peerB_connected.png)

### B. Connecting Computers Through Wi-Fi or a LAN

Peers running on separate computers must use the reachable LAN address of the remote computer instead of `127.0.0.1`.

**Step 1: Find Computer A's local IP address.**

On Windows, run:

```bash
ipconfig
```

Find the **IPv4 Address** under the active network adapter. An example address is `192.168.1.10`.

On Linux or macOS, use:

```bash
ip a
```

or:

```bash
ifconfig
```

**Step 2: Start both peers.**

For example, start a peer on Computer A using port `5000` and another on Computer B using port `5001`.

**Step 3: Connect Computer B to Computer A.**

In Computer B's application, enter:

```text
IP Address: 192.168.1.10
Port:       5000
```

Replace the example IP address with Computer A's actual LAN address, then select **Connect**.

**Step 4: Troubleshoot connection problems.**

If the connection fails, check that:

- Both computers are connected to the same Wi-Fi or LAN.
- Computer A is running the application on the specified port.
- The entered IP address and port are correct.
- The firewall allows Python to receive incoming network connections.

On Windows, check **Windows Defender Firewall → Allow an app** and permit Python where appropriate.

**Screenshots:**

- `screenshots/04_two_computers_A.png`
- `screenshots/05_two_computers_B.png`

### C. Connecting Three or More Peers

The application can maintain multiple peer connections simultaneously.

Add a third instance named Charlie and assign port `5002`. Start Alice on port `5000` and Bob on port `5001` as before.

From Charlie's window, connect to each of the following addresses:

```text
Alice: 127.0.0.1:5000
Bob:   127.0.0.1:5001
```

Click **Connect** separately for each peer.

The resulting connection layout can be represented as:

```text
       Alice (5000)
        /        \
 Bob (5001) --- Charlie (5002)
```

This example illustrates a peer-to-peer arrangement in which connections are established directly between individual peers. Charlie can communicate with both Alice and Bob, while Alice and Bob can also communicate directly when connected.

**Screenshots:**

- `screenshots/06_multi_alice.png`
- `screenshots/07_multi_bob.png`
- `screenshots/08_multi_charlie.png`

---

## 7. Sending and Receiving Text Messages

The text messaging feature allows a peer to send a message to a specific connected peer.

To send a message:

1. Find the **Connected Peers** list.
2. Select the intended recipient.
3. Enter your message in the **Send Text** field.
4. Click **Send**, or press Enter.

For example, Alice can select Bob and send:

```text
Hello Bob!
```

The sender's log displays a message similar to:

```text
Alice -> Bob: Hello Bob!
```

Bob's log identifies Alice as the sender. Replies can be sent in the same way by selecting Alice from Bob's connected-peer list.

The message destination depends on the selected peer, allowing users to communicate with different connected peers without broadcasting every message to everyone.

**Required screenshots:**

- `screenshots/09_text_A_to_B.png` — Show Alice sending a message to Bob and both peers' logs.
- `screenshots/10_text_B_to_A.png` — Show Bob sending a reply to Alice and both peers' logs.

![Text message from A to B](screenshots/09_text_A_to_B.png)

![Text message from B to A](screenshots/10_text_B_to_A.png)

---

## 8. Transferring Files Between Peers

The file-sharing feature supports ordinary files of different types, such as images, audio, video, PDFs, ZIP archives, and text documents.

### Steps to Send a File

1. Select the intended recipient in the **Connected Peers** list.
2. Click **Choose File & Send**.
3. Browse your computer and select the file.
4. Wait for the transfer to complete.
5. Check the sender's and receiver's event logs.

A successful transfer produces log messages similar to:

```text
You -> Bob: File sent: photo.jpg
```

On the receiving side, the log may show:

```text
Alice -> You: File received: photo.jpg
```

The receiver's status bar displays the location of the saved file, for example:

```text
Received: downloads\photo.jpg
```

Each received file is stored inside the receiver's `downloads/` directory. When a file with the same name already exists, the application adds a numeric suffix instead of replacing the existing file. For example:

```text
photo.jpg
photo_1.jpg
photo_2.jpg
```

### How the File Transfer Protocol Works

The transfer takes place in the following stages.

**1. Send file information**

The sender first transmits a framed JSON message containing the file's metadata. For example:

```json
{
  "type": "file",
  "filename": "photo.jpg",
  "filesize": 2456789
}
```

The actual metadata message may also contain fields identifying the sender.

**2. Transmit the file contents**

After sending the metadata, the sender transmits the file's raw binary data in chunks of **64 KB**.

The application does not need to load the entire file into memory before transmitting it.

**3. Receive the specified number of bytes**

The receiver reads the TCP stream until it has obtained exactly the number of bytes specified by `filesize`. It then writes those bytes to a file in `downloads/`.

This method is important because a single TCP `recv()` call is not guaranteed to return the complete requested data. The receiver must continue reading until the expected amount of data has arrived.

### Required File Transfer Demonstrations

Capture the sender's and receiver's logs for each transfer.

| Screenshot | Required demonstration |
|---|---|
| `screenshots/11_image_A_to_B.png` | Alice sends an image to Bob. |
| `screenshots/12_audio_B_to_A.png` | Bob sends an audio file to Alice. |
| `screenshots/13_video_A_to_B.png` | Alice sends a video file to Bob. |
| `screenshots/14_pdf_or_zip.png` | Optional: demonstrate transferring a PDF or ZIP archive. |
| `screenshots/15_downloads_folder.png` | Open the receiver's `downloads/` folder and show the files that arrived. |

Verify that the received files open or play correctly, rather than relying on the log messages alone.

---

## 9. Communication With Multiple Connected Peers

When Alice, Bob, and Charlie are connected to multiple peers, each user can select the destination independently before sending a message or file.

Examples of possible communication pairs include:

- Alice to Bob and Bob to Alice.
- Bob to Charlie and Charlie to Bob.
- Charlie to Alice and Alice to Charlie.

For text messaging, select the intended peer and send the message normally. For file sharing, choose the recipient before selecting a file.

This demonstrates that the application supports communication between different peer pairs rather than being limited to a single sender and receiver.

### Required Demonstrations

**Text communication:** Charlie sends messages to both Alice and Bob. Capture the relevant logs from all three peer windows.

**File sharing:** Alice or Bob sends a file to Charlie, followed by Charlie sending a file to another connected peer.

**Screenshots:**

- `screenshots/16_peerC_text.png`
- `screenshots/17_peerC_file.png`

---

## 10. Handling Invalid Operations and Connection Errors

The application is designed to report common errors through the GUI instead of terminating unexpectedly. The following table describes representative situations and their expected behavior.

| Situation | Expected log message or behavior |
|---|---|
| Port number outside the valid range | `[ERROR] Invalid port (must be 1-65535)` |
| Incorrect IP address | `[ERROR] Invalid IP address: ...` |
| Remote peer unavailable or connection rejected | `[ERROR] Connection failed: ... Connection refused` |
| No destination selected before sending | `[ERROR] Select a peer from the Connected Peers list first` |
| Attempting to send before starting the local peer | `[ERROR] Start your peer first` |
| Selected file does not exist | `[ERROR] File does not exist: ...` |
| File metadata contains an invalid size | The transfer is rejected, and the connection is closed safely. |
| A peer disconnects unexpectedly | A system message is recorded and the disconnected peer is removed from the list. |
| Disconnection occurs during a file transfer | The incomplete received file is deleted. |

For example, trying to connect to a port that has no active peer should produce a connection error. Similarly, stopping one peer should cause its connected peers to report the disconnection and update their lists.

### Error Handling Demonstrations

- `screenshots/18_error_connection_refused.png` — Attempt to connect to an unused port and capture the resulting error.
- `screenshots/19_peer_disconnected.png` — Stop a peer and capture the other peer's updated list and disconnection message.

---

## 11. Communication Protocol

The application uses a length-prefixed message format to identify the boundaries of JSON messages transmitted through TCP.

### Message Structure

Each framed message contains two parts:

```text
+------------------+------------------------+
| 4-byte length    | JSON payload           |
+------------------+------------------------+
```

The first four bytes indicate the length of the JSON payload. The receiver reads these bytes first, determines the expected payload size, and then continues reading until the complete payload has been received.

Length-prefix framing is necessary because TCP provides a continuous byte stream rather than preserving application-level message boundaries.

### Supported Message Types

| Message Type | Purpose | Important Fields |
|---|---|---|
| `hello` | Identifies a peer when establishing a connection. | `peer_id`, `peer_name`, `port` |
| `hello_ack` | Confirms the remote peer's identity during the handshake. | `peer_id`, `peer_name`, `port` |
| `text` | Carries a text message between connected peers. | `sender_id`, `sender_name`, `message` |
| `file` | Announces file information before the binary transfer. | `sender_id`, `sender_name`, `filename`, `filesize` |

For file messages, the framed JSON metadata is followed by the raw file bytes on the same TCP connection. The receiver must process the metadata and then read the announced number of bytes before interpreting subsequent protocol data.

### Overall Communication Sequence

The general process for establishing and using a connection is illustrated below:

```text
Peer A
   |
   | Enter remote IP address and port
   v
Create TCP connection
   |
   v
Send HELLO message
   |
   v
Exchange peer identity information
   |
   v
Connection established
   |
   v
Exchange framed JSON messages
   |
   +------> Text messages
   |
   +------> File metadata followed by binary data
   |
   v
Peer B receives and processes the data
```

This protocol provides the communication foundation used by the text messaging and file-sharing features.

---

## 12. Common Problems and Solutions

The following table provides practical solutions for common setup and usage issues.

| Problem | Possible Solution |
|---|---|
| `Could not start peer: ... address already in use` | Another process is using the chosen port. Select an unused port. |
| `Connection failed: ... Connection refused` | Check whether the remote peer is running and verify its IP address and listening port. |
| Connections work with `127.0.0.1` but fail between computers | Use the remote computer's LAN IP, ensure both computers are on the same Wi-Fi/LAN, and check firewall permissions. |
| Send action reports `Select a peer...` | Select a recipient in the **Connected Peers** list before sending. |
| A send action is attempted before startup | Select **Start Peer** first. |
| `No module named tkinter` | On Ubuntu or Debian, install Tkinter with `sudo apt install python3-tk`. |
| The lower part of the GUI is hidden | Use the latest `main.py`, which adjusts the window size to accommodate the controls. |

If a problem continues, inspect the GUI log for additional connection or file-transfer information.

---

## 13. Project Scope and Limitations

This project focuses on demonstrating basic peer-to-peer communication, TCP socket programming, concurrent connections, and file transfer in Python.

The implementation is intended for educational use and has several limitations:

- **No blockchain integration:** The application does not use blocks, transactions, distributed ledgers, or consensus algorithms.
- **No encryption:** Communication is not protected by an application-level encryption mechanism.
- **No authentication:** The handshake exchanges peer identity information but does not establish a secure, verified identity.
- **No NAT traversal:** Peers behind separate routers cannot automatically establish connections across different networks.
- **No distributed hash table (DHT):** Peers must know the address and port of another peer to initiate a connection.
- **No central server:** Connection establishment and subsequent communication take place directly between peers.
- **Network restrictions:** The simplest supported demonstrations use one computer or computers that can reach one another on a shared local network.

Communication across different networks would require additional mechanisms, such as NAT traversal, a suitable discovery mechanism, or other networking infrastructure.

These limitations keep the project focused on its main objective: demonstrating a simple, multi-peer TCP application with text communication and binary file exchange.

---

## 14. Screenshot Checklist for Demonstration and Submission

Create a folder named `screenshots/` beside `README.md`. Save the captured images using the exact filenames below so that they correspond to the documentation.

| No. | Screenshot Filename | Required Content |
|---:|---|---|
| 1 | `01_start_peer.png` | A running peer, including its name, port, peer ID, and startup log. |
| 2 | `02_peerA_connected.png` | Peer A's window showing Peer B in the connected list. |
| 3 | `03_peerB_connected.png` | Peer B's window showing Peer A in the connected list. |
| 4 | `04_two_computers_A.png` | Computer A's application displaying the connection with Computer B's LAN IP. |
| 5 | `05_two_computers_B.png` | Computer B's application displaying the connection with Computer A's LAN IP. |
| 6 | `06_multi_alice.png` | Alice's window with two connected peers. |
| 7 | `07_multi_bob.png` | Bob's window with two connected peers. |
| 8 | `08_multi_charlie.png` | Charlie's window with two connected peers. |
| 9 | `09_text_A_to_B.png` | A text message sent from A to B, with both peers' logs visible. |
| 10 | `10_text_B_to_A.png` | A text message sent from B to A, with both peers' logs visible. |
| 11 | `11_image_A_to_B.png` | An image transfer from A to B, including sender and receiver logs. |
| 12 | `12_audio_B_to_A.png` | An audio transfer from B to A, including both logs. |
| 13 | `13_video_A_to_B.png` | A video transfer from A to B, including both logs. |
| 14 | `14_pdf_or_zip.png` | Optional PDF or ZIP file transfer demonstration. |
| 15 | `15_downloads_folder.png` | The receiver's `downloads/` folder displaying the received files. |
| 16 | `16_peerC_text.png` | Charlie's text communication with other peers, with relevant logs. |
| 17 | `17_peerC_file.png` | A file transfer involving Charlie and another peer. |
| 18 | `18_error_connection_refused.png` | A connection-refused error displayed in the GUI log. |
| 19 | `19_peer_disconnected.png` | A peer disconnection message and the updated connected-peer list. |

For each demonstration, capture the actual running application. Make sure the relevant peer names, messages, transfer results, or error details are readable in the screenshots.

The optional PDF/ZIP screenshot may be omitted if that transfer is not demonstrated. All other listed screenshots cover the main project requirements.

---

## 15. Conclusion

The UAP P2P Network demonstrates how Python TCP sockets can be used to develop a simple application in which each instance can accept incoming connections and initiate outgoing ones.

Through a Tkinter interface, users can establish connections, identify peers, exchange text messages, and transfer files between multiple connected peers. Length-prefixed JSON messages, separate connection threads, and chunk-based binary transfer provide the basic mechanisms needed for communication and file sharing.

The project offers a practical introduction to networking concepts and peer-to-peer application development while leaving more advanced features—such as encryption, authentication, peer discovery, and cross-network connectivity—as possible future improvements.
