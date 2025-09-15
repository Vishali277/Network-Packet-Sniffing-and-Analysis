# Network Packet Sniffing and Analysis

This project demonstrates the critical security risks of unencrypted network traffic. Using Wireshark, this analysis captures and exposes plaintext login credentials sent over the HTTP protocol, highlighting the necessity of modern encryption standards like HTTPS.

---

## 🚀 Setup and Usage

This guide provides the steps needed to reproduce this network analysis experiment.

### **Prerequisites**

* A Linux environment (Kali Linux is recommended as it includes all necessary tools).
* **Wireshark**: A network protocol analyzer. Install via `sudo apt update && sudo apt install wireshark`.
* **Root/Administrator Privileges**: Required to capture network packets.

### **Reproduction Steps**

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

## 🧪 Testing and Validation

To validate the findings and explore edge cases, the following tests were conducted.

* **Scenario 1: Testing a Secure (HTTPS) Website**
    * **Test**: The same capture process was repeated on a secure login page, such as `https://github.com/login`.
    * **Result**: Applying the `http.request.method == "POST"` filter yielded **no results**. The traffic is not plain HTTP. When inspecting the raw traffic, all application data was encapsulated within the **"Transport Layer Security" (TLS)** protocol, appearing as unreadable "Application Data."
    * **Validation**: This test **confirms the effectiveness of HTTPS**. The TLS encryption successfully prevents sniffers from reading the contents of the communication, even though packets are still being captured. The sensitive login data is protected.

* **Scenario 2: Using an Incorrect Filter**
    * **Test**: A filter for a different protocol, such as `ftp`, was applied to the capture.
    * **Result**: The packet containing the HTTP login credentials disappeared from view.
    * **Validation**: This validates the **importance and precision of Wireshark's filtering engine**. Effective analysis requires knowing what protocols to look for; otherwise, critical data can be easily missed in the noise of thousands of packets.

---

## ⚙️ Troubleshooting and Error Handling

This section documents potential issues and their solutions.

* **Error: "You don't have permission to capture on that device"**
    * **Cause**: Wireshark was not launched with `sudo` or root privileges.
    * **Solution**: Close Wireshark and relaunch it from the terminal using `sudo wireshark`.

* **Issue: No interfaces are listed, or `eth0` shows no traffic.**
    * **Cause**: This typically indicates a network configuration issue within the virtual machine. The VM's network adapter may be disconnected or misconfigured.
    * **Solution**: Check the VM's settings and ensure the network adapter is enabled and set to "NAT" or "Bridged Mode" with a working internet connection.

* **Issue: The `http.request.method == "POST"` filter returns nothing.**
    * **Cause**: This can happen for several reasons:
        1.  The capture was stopped *before* the login form was submitted.
        2.  The user accidentally navigated to an `https` version of a website.
        3.  There was a typo in the filter string.
    * **Solution**: Ensure the URL begins with `http://`. Verify the capture is running when you click "login." Carefully type or copy-paste the filter.

---

## 📝 Analysis Report

(Your original analysis report was great and can be placed here.)

### Identified Security Issues

* **Critical Issue: Plaintext Credential Exposure**
    * **Description**: The analysis revealed that the username and password were transmitted over the network in plaintext within an HTTP POST request.
    * **Risk**: Anyone on the same local network could intercept this traffic and steal the credentials, leading to account compromise.
    * **Recommendation**: Implement site-wide **HTTPS** to encrypt all communication between the client and server, protecting data in transit.
