# Install Jenkins on GCP Compute Engine (Ubuntu)

This guide explains how to install Jenkins on a Google Cloud Platform (GCP) Compute Engine VM and access the Jenkins dashboard through a web browser.

## Architecture

- **Cloud Provider:** Google Cloud Platform (GCP)
- **Compute Service:** Compute Engine
- **Operating System:** Ubuntu 24.04 LTS
- **CI/CD Tool:** Jenkins LTS
- **Java Runtime:** OpenJDK 21
- **Jenkins Port:** TCP 8080

## Step 1: Create a GCP Virtual Machine

1. Log in to the [Google Cloud Console](https://console.cloud.google.com/).
2. Navigate to **Compute Engine → VM instances**.
3. Click **Create instance**.
4. Configure the VM with the following settings:

| Setting | Value |
|---|---|
| Name | `jenkins-server` |
| Region | `asia-south1` (Mumbai) |
| Machine type | `e2-medium` |
| Operating system | Ubuntu 24.04 LTS |
| Boot disk | 20–30 GB |
| External IP | Enabled |

5. Click **Create**.
6. Once the VM is running, click **SSH** to connect to it.

## Step 2: Configure the GCP Firewall

Jenkins uses TCP port `8080` by default for its web interface.

1. Navigate to **VPC network → Firewall**.
2. Click **Create firewall rule**.
3. Configure the rule as follows:

| Field | Value |
|---|---|
| Name | `allow-jenkins-8080` |
| Direction | Ingress |
| Action | Allow |
| Targets | Specified target tags |
| Target tag | `jenkins-server` |
| Source IPv4 ranges | Your public IP followed by `/32` |
| Protocol | TCP |
| Port | `8080` |

4. Save the firewall rule.
5. Add the network tag `jenkins-server` to your VM under **Edit → Network tags**.

> **Security:** Restrict access to your own public IP. Avoid exposing Jenkins to all internet addresses (`0.0.0.0/0`) unless you have a specific, secured reason.

## Step 3: Update Ubuntu Packages

Run the following commands in the VM's SSH terminal:

```bash
sudo apt update
sudo apt upgrade -y
```

## Step 4: Install Java

Jenkins requires a supported Java runtime.

```bash
sudo apt install -y fontconfig openjdk-21-jre
```

Verify the installation:

```bash
java -version
```

Expected output should show Java 21.

## Step 5: Add the Jenkins Repository

Create the directory for repository keys:

```bash
sudo mkdir -p /etc/apt/keyrings
```

Download the Jenkins repository signing key:

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
```

Add the Jenkins LTS repository:

```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" \
| sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

Update the package index:

```bash
sudo apt update
```

## Step 6: Install Jenkins

Install Jenkins:

```bash
sudo apt install -y jenkins
```

## Step 7: Start and Enable Jenkins

Start the Jenkins service:

```bash
sudo systemctl start jenkins
```

Enable Jenkins to start automatically after a reboot:

```bash
sudo systemctl enable jenkins
```

Check the service status:

```bash
sudo systemctl status jenkins
```

Confirm that the service displays:

```text
Active: active (running)
```

## Step 8: Verify Port 8080

Check whether Jenkins is listening on port 8080:

```bash
sudo ss -tulnp | grep 8080
```

If Jenkins is running successfully, you should see a listening socket for port 8080.

## Step 9: Access Jenkins in a Browser

1. Open **GCP → Compute Engine → VM instances**.
2. Copy the external IP address of your VM.
3. Open the following URL in your browser:

```text
http://YOUR_EXTERNAL_IP:8080
```

Replace `YOUR_EXTERNAL_IP` with the actual external IP address of your VM.

## Step 10: Unlock Jenkins

Run the following command in the VM terminal:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

1. Copy the initial administrator password.
2. Paste it into the Jenkins setup page.
3. Click **Install suggested plugins**.
4. Create your administrator account.
5. Complete the setup wizard.

You should now be able to access the Jenkins dashboard.

## Troubleshooting

### Jenkins service is not running

```bash
sudo systemctl status jenkins
sudo journalctl -u jenkins --no-pager -n 50
```

### Port 8080 is not listening

```bash
sudo ss -tulnp | grep 8080
```

Restart Jenkins if necessary:

```bash
sudo systemctl restart jenkins
```

### Browser cannot connect to Jenkins

Check the following:

- The VM is running.
- The VM has an external IP address.
- The GCP firewall rule allows ingress TCP traffic on port 8080.
- The firewall rule's target tag matches the VM.
- Jenkins is listening on port 8080.
- Any applicable Ubuntu firewall or network restrictions allow the connection.

### Java compatibility issue

```bash
java -version
sudo journalctl -u jenkins --no-pager -n 100
```

Confirm that the installed Java version is supported by the Jenkins version you installed.

## Useful Jenkins Commands

| Task | Command |
|---|---|
| Start Jenkins | `sudo systemctl start jenkins` |
| Stop Jenkins | `sudo systemctl stop jenkins` |
| Restart Jenkins | `sudo systemctl restart jenkins` |
| Check status | `sudo systemctl status jenkins` |
| Enable at boot | `sudo systemctl enable jenkins` |
| View logs | `sudo journalctl -u jenkins -f` |
| Get initial password | `sudo cat /var/lib/jenkins/secrets/initialAdminPassword` |

## Official Documentation

- [Jenkins Installation Guide](https://www.jenkins.io/doc/book/installing/linux/)
- [Google Cloud Compute Engine Documentation](https://cloud.google.com/compute/docs)
- [Google Cloud Firewall Rules](https://cloud.google.com/firewall/docs/firewalls)

## Conclusion

Jenkins is now installed on a GCP Compute Engine Ubuntu VM. You can use it to create CI/CD pipelines, automate builds, run tests, and deploy applications.

**Next steps:** Configure GitHub integration, create a Jenkins pipeline, and securely manage credentials for your deployment workflow.
