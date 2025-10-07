# **Sysmon Setup**

## **Objective**

Install and configure Sysmon on a Windows Server.

---

## **Tools Used**

* **Sysmon (Sysmon64.exe)** – For monitoring and capturing system activity.
* **Sysmon Configuration File (sysmonconfig.xml)** – Defines what events and activities Sysmon records.
* **Windows Event Viewer** – Used to validate Sysmon event logs.
* **Elasticsearch** – For ingesting and analyzing Sysmon and Windows Defender logs.
* **Windows Server & PowerShell** – For installing, configuring, and managing Sysmon.

---

## **Sysmon Installation Steps**

1. On your **Windows Server**, download:

   * **Sysmon**
   * The **Sysmon Olaf Configuration** from GitHub
     (save the raw file as `sysmonconfig.xml`).

2. Open **PowerShell** as Administrator and navigate to the Sysmon directory to confirm the XML file and configurations.

3. Run the installation command:

   ```powershell
   .\Sysmon64.exe -i sysmonconfig.xml
   ```

   * This will prompt a license agreement to start Sysmon installation.

4. Once installed, confirm Sysmon’s presence in:

   * **Windows Services**
   * **Event Viewer**

---

## **Viewing Sysmon Events**

Open **Event Viewer → Applications and Services Logs → Microsoft → Windows → Sysmon → Operational**.

Key Event IDs include:

* **Event ID 1** – Process creation
* **Event ID 3** – Network connections
* **Event ID 7** – Image loaded events in specific processes

These logs confirm Sysmon is actively capturing system events.

---

## **Ingesting Sysmon and Windows Defender Logs into Elasticsearch**

### **Step 1: Add Sysmon Integration**

1. On your **Elasticsearch** homepage, click **Add Integration**.
2. Search for **Custom Windows Event Logs** and click **Add Custom Windows Event Logs**.
3. Provide an **integration name** and **description**.
4. The **channel name** is obtained from your Windows Server:

   * Open **Event Viewer** → **Applications and Services Logs** → **Microsoft → Windows → Sysmon → Operational**.
   * Right-click **Operational**, go to **Properties**, and copy the **Full Name** (this is your channel name).
5. Paste the channel name into Elasticsearch.
6. Assign the existing host to the **Zic-Windows-Policy** agent policy.
7. Click **Save and Continue**, then **Save and Deploy**.

✅ Sysmon is now successfully integrated into Elasticsearch.

---

### **Step 2: Add Windows Defender Integration**

Repeat the same process as Sysmon, with the following adjustments:

1. When prompted for **channel name**, navigate to:

   ```
   Event Viewer → Applications and Services Logs → Microsoft → Windows → Windows Defender → Operational
   ```

   Copy the **Full Name** and paste it as the channel name.

2. Under **Advanced Options**, specify the following Event IDs:

   ```
   1116, 1117, 5001
   ```

   * **1116** – Detects malware or unwanted software
   * **1117** – Performs actions to protect against malware
   * **5001** – Indicates real-time protection is disabled

3. Select **Zic-Windows-Policy** under existing hosts.

4. Click **Save and Continue**, then **Save and Deploy**.

 You should see a notification:

> “Zic-win-Defender Integration added.”

This confirms that **Windows Defender logs** are now being ingested into Elasticsearch.

---

## **Verification**

* Check Elasticsearch to see if specified **Event IDs** (e.g., 1, 1116, 1117, 5001) appear in the logs.
* Expand an event to view details such as:

  * **Event Provider**
  * **Event Code**
  * **Hostname**

---

## **Use of Sysmon**

* Tracks **process creation** (Event ID 1).
* Monitors **network connections** (Event ID 3).
* Logs **image loads and DLL activity** (Event ID 7).
* Provides **forensic and threat-hunting data** to detect malicious behavior.
* Feeds **structured telemetry** into SIEM tools like **Elasticsearch** for real-time analysis.

---

## **Challenge Faced**

During setup, **Microsoft Edge** on the Windows Server returned errors when searching for Sysmon.
To resolve this, **Google Chrome** was installed and configured, allowing successful Sysmon downloads.

---
