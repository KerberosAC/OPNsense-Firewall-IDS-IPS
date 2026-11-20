
# OPNsense Firewall & IDS/IPS

**Network Security | Intrusion Detection | Web Filtering | Firewall Administration**

## Objective

The objective of this project was to design and implement a virtualized network security environment using OPNsense, Suricata, and Squid Proxy.

The project focused on configuring firewall policies, monitoring network traffic, detecting suspicious activity, and implementing web filtering and access controls.

A controlled environment was developed using VMware Workstation Pro, with Kali Linux serving as a security testing machine and Windows 10 acting as a client endpoint.

The project provided practical experience in network defense, intrusion detection and prevention concepts, custom security rule configuration, SSL inspection, and troubleshooting network security infrastructure.

### Skills Learned

- Deploying and configuring OPNsense firewall infrastructure.
- Managing WAN and LAN interfaces, IP addressing, and network connectivity.
- Creating firewall rules, NAT policies, and access controls.
- Configuring Suricata for network intrusion detection.
- Developing and deploying custom Suricata detection rules.
- Simulating network reconnaissance using Nmap.
- Investigating intrusion detection alerts and tuning detection thresholds.
- Implementing Squid Proxy for web filtering and traffic management.
- Configuring SSL inspection certificates and trusted root authorities.
- Troubleshooting network connectivity, certificate trust, and proxy configuration.
- Validating security controls through controlled testing.

### Tools Used

- **OPNsense:** Firewall administration, network routing, and security policy enforcement.
- **Suricata:** Network intrusion detection and prevention engine.
- **Squid Proxy:** HTTP/HTTPS proxy, web filtering, and access control.
- **VMware Workstation Pro 17:** Virtualized lab infrastructure.
- **Kali Linux:** Security testing and simulated reconnaissance.
- **Windows 10:** Client endpoint for proxy and firewall testing.
- **Nmap:** Network scanning and intrusion detection validation.
- **OpenSSL:** Certificate conversion and SSL inspection configuration.
- **FileZilla:** File transfer for custom security configuration files.
- **Python HTTP Server:** Hosting custom Suricata rules for retrieval.

---

## Lab Environment

The environment consisted of three virtual machines connected through a controlled network.

| Component | Role | IP Address |
|---|---|---|
| OPNsense | Firewall and security gateway | 10.200.200.254 |
| Kali Linux | Security testing workstation | 10.200.200.17 |
| Windows 10 | Protected client endpoint | 10.200.200.16 |
| Network | Internal lab subnet | 10.200.200.0/24 |
| Hypervisor | VMware Workstation Pro 17 | — |

OPNsense was configured with separate WAN and LAN interfaces to manage traffic between the internal network and external connectivity.

**Lab Scope:** All security testing was conducted within the virtual lab environment.

---

## Steps

### 1. Virtual Lab Deployment

The project began by deploying OPNsense, Kali Linux, and Windows 10 virtual machines using VMware Workstation Pro.

OPNsense was configured to operate as the network gateway, while Kali Linux and Windows 10 were used to generate and evaluate network traffic.

The virtual machines were connected through the designated lab network to support controlled testing.

![Virtual Lab Environment](images/01-lab-environment.png)

*Ref 1: VMware virtual lab containing OPNsense, Kali Linux, and Windows 10.*

### 2. OPNsense Network Configuration

The OPNsense firewall was configured with separate WAN and LAN network interfaces.

The LAN interface was assigned:

```text
IP Address: 10.200.200.254
Subnet Mask: 255.255.255.0
Network: 10.200.200.0/24
```

This address served as the default gateway for the internal virtual machines.

The OPNsense management interface was accessed through:

```text
https://10.200.200.254
```

![OPNsense Interface Configuration](images/02-opnsense-interfaces.png)

*Ref 2: OPNsense network interface configuration showing the LAN gateway.*

### 3. Kali Linux Network Configuration

Kali Linux was configured with a static IP address to communicate with the OPNsense firewall.

The network configuration was updated using:

```bash
sudo nano /etc/network/interfaces
```

The lab configuration assigned the following network settings:

```text
auto eth1
iface eth1 inet static
    address 10.200.200.17
    netmask 255.255.255.0
    gateway 10.200.200.254
```

Connectivity was validated using:

```bash
ping 10.200.200.254
```

This resolved an initial networking issue in which Kali Linux was not correctly configured on the OPNsense LAN subnet.

![Kali Linux Network Configuration](images/03-kali-network.png)

*Ref 3: Kali Linux static IP configuration and connectivity testing.*

### 4. Windows 10 Client Configuration

The Windows 10 virtual machine was configured as the protected client endpoint.

Its network settings included:

```text
IP Address: 10.200.200.16
Subnet Mask: 255.255.255.0
Default Gateway: 10.200.200.254
```

The client was used to evaluate firewall policies, web filtering, proxy access controls, and certificate trust.

![Windows Network Configuration](images/04-windows-network.png)

*Ref 4: Windows 10 network configuration for the protected lab client.*

### 5. Firewall Rules and Traffic Control

Firewall policies were configured in OPNsense to control traffic originating from the internal network.

The configuration included rules for:

- Permitting authorized network communications.
- Redirecting web traffic to the Squid proxy.
- Restricting direct access that bypassed proxy controls.
- Logging traffic associated with blocked connections.

The lab documentation describes proxy redirection using local proxy ports 3128 for HTTP and 3129 for HTTPS.

Rule ordering was considered to ensure restrictive policies were evaluated before general allow rules.

![OPNsense Firewall Rules](images/05-firewall-rules.png)

*Ref 5: OPNsense firewall rules configured for network access control and proxy traffic management.*

### 6. Suricata Intrusion Detection Configuration

Suricata was enabled within OPNsense to monitor network traffic for suspicious activity.

The intrusion detection configuration included:

- Enabling the Suricata detection engine.
- Selecting the relevant network interface.
- Configuring the internal network scope.
- Enabling promiscuous mode for traffic inspection.
- Downloading and activating detection rulesets.

The internal network was configured around the lab subnet:

```text
10.200.200.0/24
```

Suricata was used to identify suspicious network activity and generate security alerts.

![Suricata IDS Configuration](images/06-suricata-settings.png)

*Ref 6: Suricata intrusion detection settings configured within OPNsense.*

### 7. Custom Suricata Rule Deployment

Custom Suricata detection rules were developed to identify network reconnaissance activity.

The lab used custom configuration files, including:

```text
custommap.xml
custommap.rules
```

These files were transferred and configured for use with OPNsense.

A Python HTTP server was also used on Kali Linux to make custom rules accessible over the lab network.

```bash
sudo python3 -m http.server 80
```

The rules were hosted from the Kali Linux system at:

```text
http://10.200.200.17/
```

The custom ruleset was then incorporated into the Suricata configuration.

![Custom Suricata Rules](images/07-custom-suricata-rules.png)

*Ref 7: Custom Suricata rule configuration used to detect network reconnaissance activity.*

### 8. Nmap Intrusion Detection Testing

To validate the intrusion detection configuration, an Nmap SYN scan was executed from Kali Linux against the OPNsense firewall.

The following command was used:

```bash
sudo nmap -sS -Pn --top-ports 500 10.200.200.254
```

**Command Breakdown**

| Parameter | Purpose |
|---|---|
| `sudo` | Executes the command with elevated privileges |
| `-sS` | Performs TCP SYN scanning |
| `-Pn` | Skips host discovery |
| `--top-ports 500` | Scans the 500 most common ports |
| `10.200.200.254` | OPNsense target IP address |

This simulated reconnaissance activity against the lab firewall.

![Nmap Scan Execution](images/08-nmap-scan.png)

*Ref 8: Kali Linux executing an Nmap SYN scan against the OPNsense firewall.*

### 9. Intrusion Detection Alert Analysis

Following the Nmap scan, Suricata alerts were reviewed to determine whether the custom detection rules identified the reconnaissance activity.

The custom rule was designed to generate an alert similar to:

```text
POSSIBLE NMAP SYNSTEALTH SCAN DETECTED
```

During testing, adjustments were required to address rule loading and detection threshold behavior.

The configuration was refined to improve the visibility of the simulated scanning activity.

![Suricata Detection Alerts](images/09-suricata-alerts.png)

*Ref 9: Suricata intrusion detection alerts generated during controlled Nmap testing.*

**Detection vs. Prevention:** The documented Nmap test validates intrusion detection. It does not independently establish that Suricata dropped or prevented the scan traffic.

### 10. Squid Proxy Configuration

Squid Proxy was configured within OPNsense to provide centralized web traffic management and filtering.

The proxy was used to:

- Process web requests from the Windows 10 client.
- Apply access control policies.
- Restrict access to selected websites.
- Support traffic logging and inspection.
- Enforce network browsing restrictions.

The lab also configured firewall policies intended to prevent clients from bypassing proxy controls.

![Squid Proxy Configuration](images/10-squid-proxy.png)

*Ref 10: Squid Proxy configuration within the OPNsense firewall.*

### 11. Web Filtering and Access Control

Access Control Lists (ACLs) were configured to restrict access to selected website categories and destinations.

The lab tested web filtering using a Windows 10 browser.

One documented test involved attempting to access Facebook.

The Squid proxy returned an **Access Denied** response, demonstrating that the configured access control policy was applied.

An initial ACL database issue was resolved by using an alternative compatible blacklist source.

![Squid Web Filtering](images/11-web-filtering.png)

*Ref 11: Windows 10 browser displaying a Squid access-denied response during web filtering validation.*

### 12. SSL Inspection and Certificate Configuration

SSL inspection was configured to support inspection of encrypted web traffic in the controlled lab environment.

A certificate was generated through OPNsense and exported for installation on the Windows 10 client.

The exported certificate was converted from `.p12` to `.crt` format using OpenSSL.

A representative certificate extraction command is:

```bash
openssl pkcs12 -in proxy-ca.p12 -clcerts -nokeys -out proxy-ca.crt
```

The certificate was then imported into the Windows Trusted Root Certification Authorities store.

This allowed the Windows client to recognize the lab's trusted proxy certificate.

During troubleshooting, the proxy configuration was adjusted to use HTTP for the proxy connection rather than attempting to connect to the proxy itself over HTTPS.

![SSL Certificate Configuration](images/12-ssl-certificate.png)

*Ref 12: Certificate conversion and installation process for Squid SSL inspection.*

### 13. Troubleshooting and Configuration Refinement

Several configuration issues were encountered during deployment and testing.

| Issue | Resolution |
|---|---|
| Kali Linux network connectivity | Configured static IP addressing on the correct interface |
| Windows client connectivity | Reviewed gateway, subnet, and virtual network configuration |
| Suricata rules not loading correctly | Corrected custom ruleset configuration and deployment |
| Nmap alerts not appearing consistently | Adjusted detection rules and thresholds |
| Squid blacklist update failures | Replaced the ACL source with a compatible blacklist |
| Certificate trust errors | Converted and imported the certificate into Windows |
| Proxy connection issues | Corrected HTTP proxy connection settings |
| OPNsense update problems | Adjusted IPv6 configuration during troubleshooting |

These challenges provided practical experience in troubleshooting integrated network security systems.

![Troubleshooting and Validation](images/13-troubleshooting.png)

*Ref 13: Configuration troubleshooting and validation of network security services.*

---

## Results and Findings

The project demonstrated the integration of multiple security controls within a single virtualized network.

### Key Results

- Deployed an OPNsense firewall within a VMware virtual environment.
- Configured a dedicated LAN network with Kali Linux and Windows 10 endpoints.
- Established OPNsense as the network gateway.
- Implemented firewall policies for traffic management and proxy access.
- Configured Suricata intrusion detection and custom detection rules.
- Simulated network reconnaissance using Nmap SYN scanning.
- Investigated intrusion detection alerts and refined rule thresholds.
- Configured Squid Proxy for web filtering and access control.
- Validated web filtering through a Squid access-denied response.
- Configured certificate trust for SSL inspection testing.
- Resolved networking, proxy, ruleset, and certificate configuration issues.

### Security Controls Implemented

| Security Control | Purpose |
|---|---|
| OPNsense Firewall | Network access control and traffic management |
| Suricata IDS | Detection of suspicious network activity |
| Custom Detection Rules | Identification of simulated reconnaissance |
| Squid Proxy | Centralized web traffic filtering |
| Access Control Lists | Restriction of selected web destinations |
| SSL Inspection | Controlled inspection of encrypted web traffic |
| Firewall Logging | Visibility into blocked and permitted traffic |

### Key Takeaways

**Layered network defense improves visibility and control.**

Combining firewall policies, intrusion detection, and proxy filtering provided multiple opportunities to monitor and restrict network activity.

**Custom detection rules require validation and tuning.**

Developing a rule is only one part of intrusion detection engineering. Effective deployment also requires testing, alert analysis, and threshold adjustments.

**Security infrastructure depends on correct network configuration.**

Incorrect interface addressing, gateway settings, or virtual network configuration can prevent security tools from communicating effectively.

**Certificate management is essential for encrypted traffic inspection.**

SSL inspection requires carefully configured trust relationships and appropriate controls to protect user privacy and maintain secure communications.

---

## Conclusion

This project provided hands-on experience in deploying and managing a layered network security environment using OPNsense, Suricata, and Squid Proxy.

The integration of firewall administration, custom intrusion detection rules, controlled network reconnaissance, web filtering, and SSL inspection strengthened my understanding of defensive network security operations.

Troubleshooting connectivity issues, detection rule behavior, proxy configurations, and certificate trust also reinforced the importance of systematic problem-solving in cybersecurity.

The project demonstrates practical skills relevant to network security engineering, security operations, intrusion detection, and firewall administration.

---

## References

- [OPNsense Official Documentation](https://docs.opnsense.org/)
- [OPNsense Intrusion Prevention Documentation](https://docs.opnsense.org/manual/ips.html)
- [OPNsense Firewall Rules](https://docs.opnsense.org/manual/firewall.html)
- [OPNsense Web Proxy Documentation](https://docs.opnsense.org/manual/proxy.html)
- [Suricata Documentation](https://docs.suricata.io/)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Squid Proxy Documentation](https://www.squid-cache.org/Doc/)
- [OpenSSL Documentation](https://docs.openssl.org/)
