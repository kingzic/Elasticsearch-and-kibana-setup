# **Elastic Agent and Fleet Server Setup**

## **Objective**

How to install Elastic Agent on a Windows server and enroll the server into a Fleet Server.

---

## **Tools Used**

* **Elastic Agent** – Installed on servers to collect and forward data to Fleet Server.
* **Fleet Server** – Acts as the central service that manages Elastic Agents and ensures policies are applied.
* **Elasticsearch & Kibana** – Provide indexing, storage, visualization, and management dashboards.
* **Firewall & Networking Tools** (`ufw`, IP rules, ports `9200` & `8220`) – Control network traffic and enable communication between servers.
* **PowerShell & Linux SSH** – Used for running installation and enrollment commands on Windows and Linux environments.

> **Note:** When deploying a new server under the same Virtual Private Cloud (VPC), ensure the location remains the same.

---

## **Setup Process**

1. **Access Fleet in Elasticsearch**

   * Open your **Elasticsearch** instance.
   * Click the hamburger menu → go to **Management** → select **Fleet** → click **Add Fleet Server**.
   * Include your Fleet Server name and the IP address of the Fleet Server created in the cloud.
   * For the URL, use the format:

     ```
     https://<IP-address>
     ```

     This ensures a Fleet Server policy can be generated.

---

## **Installing the Fleet Server**

1. Choose **Linux arm64** (or the appropriate architecture) for installation.

   * If a syntax error occurs, ensure you selected the correct distribution (e.g., `linux-x86_64`).

2. **Configure Firewall Rules**

   * Allow the Fleet Server IP address.
   * Allow Kibana’s port:

     ```bash
     ufw allow 9200
     ```
   * Run the command within the Kibana directory.

3. After configuration, the **Elastic Agent** should install successfully and the **Fleet Server** should connect properly.

---

## **Adding an Elastic Agent**

1. In **Fleet**, click **Add Agent**.
2. Rename the new agent (e.g., `Zic Windows Policy`).
3. In **Add Elastic Agent to Your Host**, select **Windows**.
4. Copy the generated command and run it on your Windows cloud server with administrative privileges:

   * Open **PowerShell** as Administrator.
   * Paste and execute the copied command.

---

## **Troubleshooting**

### **1. Fleet Server Connection Error**

* **Error:** `Failed to execute request to Fleet Server`
* **Cause:** Fleet Server runs on port `8220`, but the setup used port `443`.
* **Fix:**

  ```bash
  ufw allow 8220
  ```

  Then go to:

  * **Elasticsearch → Management → Fleet → Settings**
  * Change port from `443` to `8220`
  * **Save and apply settings → Save and deploy**

### **2. Remove Previous Elastic Agent Installation**

Run the following commands from the default directory `C:\Users\Administrator`:

```powershell
Stop-Service -Name "Elastic Agent"
sc.exe delete "Elastic Agent"
Remove-Item -Path "C:\Program Files\Elastic\Agent" -Recurse -Force -ErrorAction SilentlyContinue
```

### **3. Certificate Error**

* **Error:** `x509: certificate signed by unknown authority`
* **Fix:** Append the `--insecure` flag to the enrollment command and rerun it.

---

## **Verification**

Once the Elastic Agent installs successfully:

* The Windows policy will appear on the Fleet Server.
* Go to **Fleet → Zic-Network → Logs**, and verify logs are being generated.

  * This confirms that the Windows server is integrated into Fleet.

You can also search for `Zic Windows` in Elasticsearch logs to verify visibility and event data (event codes, categories, etc.).

---

## **Use of Elastic Agent and Fleet Server**

* **Elastic Agent:**
  A unified agent that collects logs, metrics, and endpoint security data from hosts, forwarding them to Fleet Server. It replaces multiple Beats agents with one streamlined solution.

* **Fleet Server:**
  The central service that coordinates Elastic Agents, manages enrollment, distributes configuration policies, and ensures secure communication with Elasticsearch. It acts as the control point for large-scale deployments.

---

## **Challenges Faced**

* Syntax errors from incorrect Linux distribution choice.
* Firewall misconfigurations (blocked ports or incorrect IP rules).
* Wrong port usage (e.g., Fleet Server requires `8220` instead of `443`).
* Certificate issues (`x509` errors) requiring the `--insecure` flag.
* Failed agent enrollment due to misconfigured firewall or IP restrictions.

---
