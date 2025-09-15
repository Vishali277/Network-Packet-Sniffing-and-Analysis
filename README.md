# Network Packet Sniffing and Analysis

This project demonstrates the critical security risks of unencrypted network traffic. Using Wireshark, this analysis captures and exposes plaintext login credentials sent over the HTTP protocol, highlighting the necessity of modern encryption standards like HTTPS.

---

##  Setup and Usage

This section provides a complete guide on how to set up the environment and reproduce the project's results.

### **Prerequisites**

* A Linux environment (Kali Linux is recommended as it includes all necessary tools).
* **Wireshark**: A network protocol analyzer. Install via `sudo apt update && sudo apt install wireshark`.
* **Root/Administrator Privileges**: Required to capture network packets.

### **Instructions**

1.  **Launch Wireshark**: Open a terminal and run Wireshark with root privileges.
    ```bash
    sudo wireshark
    ```

2.  **Start Capture**: In the Wireshark interface, identify your primary network interface (usually `eth0` in a VM) and double-click it to begin capturing live traffic.

3.  **Generate Insecure Traffic**: Open a web browser and navigate to the insecure test site: `http://testphp.vulnweb.com/login.php`.

4.  **Submit Credentials**: Enter any credentials (e.g., `testuser` / `password123`) and submit the form.

5.  **Stop Capture**: Return to Wireshark and click the red "Stop" button.

6.  **Filter for POST Requests**: In the display filter bar, apply the following filter to isolate the packet containing the submitted form data:
    ```
    http.request.method == "POST"
    ```

7.  **Analyze the Packet**: Select the filtered packet and inspect the "Packet Details" pane. Expand the "Hypertext Transfer Protocol" section to view the captured credentials in plaintext.

---

##  Testing and Validation

To ensure the methods are robust and to understand their limitations, the following tests were conducted.

* **Scenario 1: Testing an Insecure (HTTP) Website**
    * **Test:** Captured traffic from a login form submitted over `http`.
    * **Result:** The credentials `testuser` and `password123` were successfully captured and were fully readable in plaintext.
    * **Validation:** This confirms that the tool and method work correctly for intercepting unencrypted data.

* **Scenario 2: Testing a Secure (HTTPS) Website**
    * **Test**: The same capture process was repeated on a secure login page, such as `https://github.com/login`.
    * **Result**: Applying the `http.request.method == "POST"` filter yielded **no results**. The packet's payload was encrypted by the **"Transport Layer Security" (TLS)** protocol.
    * **Validation**: This test **validates the effectiveness of HTTPS**. The encryption successfully prevents sniffers from reading sensitive data, confirming it as the primary defense against this type of attack.

---

##  Troubleshooting / Common Errors

This section documents potential issues a user might encounter and how to resolve them.

* **Error: "You don't have permission to capture on that device"**
    * **Cause**: Wireshark was not launched with `sudo` or root privileges.
    * **Solution**: Close Wireshark and relaunch it from the terminal using `sudo wireshark`.

* **Issue: No interfaces are listed, or `eth0` shows no traffic.**
    * **Cause**: This typically indicates a network configuration issue within a virtual machine.
    * **Solution**: Check the VM's settings and ensure the network adapter is enabled and connected (e.g., set to "NAT" or "Bridged Mode").

* **Issue: The filter `http.request.method == "POST"` returns nothing.**
    * **Cause**: The capture was stopped before the login form was submitted, or the user accidentally visited an `https` website.
    * **Solution**: Ensure the URL begins with `http://` and that the capture is running when you click the "login" button.

---

##  Analysis and Findings

This section details the results discovered during the packet capture and analysis session.

* **Finding 1: Plaintext Credentials Intercepted**
    The most significant finding was the successful interception of user credentials sent in **plaintext**. The captured **HTTP POST request** contained the form data `uname=testuser` and `pass=password123`, which were fully readable within the packet payload.

* **Finding 2: Inherent Insecurity of the HTTP Protocol**
    The analysis confirms that the standard **HTTP protocol is unencrypted**. All data exchanged between the client's browser and the web server, including sensitive information submitted in forms, is transmitted as cleartext.

* **Finding 3: Ease of Data Interception**
    The experiment demonstrated that intercepting this data is trivial for anyone on the same local network. Using a common and readily available tool like **Wireshark**, it took only a few minutes to capture, filter, and expose the sensitive login information.

---

##  Key Learnings and Defense Strategies

* **Key Learnings:** The primary takeaway is the shocking ease and speed with which unencrypted data can be intercepted on a network. This highlights the critical difference in security between HTTP and HTTPS.
* **Defense Strategies:** The definitive defense is to use encryption.
    * **For Websites:** Implement site-wide **HTTPS** to encrypt all data in transit.
    * **For Users:** Avoid submitting sensitive information on unencrypted (`http://`) sites, refrain from using untrusted public Wi-Fi, and consider using a **VPN** to encrypt all network traffic.

---

##  Files in Repository

* `insecure_login.pcapng`: The Wireshark packet capture file containing the intercepted traffic.
* `screenshot-wireshark-filter.png`: Screenshot of the Wireshark interface showing the POST filter.
* `screenshot-plaintext-credentials.png`: Detailed screenshot of the packet details pane showing the exposed credentials.
