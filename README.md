# Zero-Trust Architecture with Ansible Automation

## Overview

This repository demonstrates how to implement a comprehensive zero-trust security model for Red Hat Enterprise Linux (RHEL) systems using Ansible automation. Zero-trust architecture represents a fundamental shift from traditional perimeter-based security to a model where every access request is verified, regardless of location or user credentials.

## What is Zero-Trust Architecture?

Zero-trust is a security framework that operates on the principle **"never trust, always verify."** Unlike traditional security models that establish a trusted perimeter and assume everything inside is safe, zero-trust treats every user, device, application, and network flow as potentially compromised.

![Zero-Trust RHEL Architecture](/images/diagram.svg)

### Core Zero-Trust Principles

1. **Never Trust, Always Verify**: Every access request must be authenticated and authorized
2. **Principle of Least Privilege**: Users and systems receive minimal access required for their function
3. **Assume Breach**: Design systems assuming adversaries are already present
4. **Verify Explicitly**: Use all available data points for access decisions
5. **Continuous Monitoring**: Monitor and log all activities in real-time
6. **End-to-End Encryption**: Protect data in transit and at rest

## Benefits of Zero-Trust Architecture

### Security Benefits

**Enhanced Threat Detection**
- Continuous monitoring enables rapid identification of anomalous behavior
- Granular logging provides detailed audit trails for forensic analysis
- Real-time alerting reduces mean time to detection (MTTD)

**Reduced Attack Surface**
- Micro-segmentation limits lateral movement of threats
- Least privilege access minimizes potential damage from compromised accounts
- Network segmentation isolates critical systems and data

**Improved Incident Response**
- Detailed logs and monitoring data accelerate incident investigation
- Automated response capabilities can contain threats immediately
- Clear audit trails support compliance and forensic requirements

**Protection Against Advanced Threats**
- Defends against insider threats and compromised credentials
- Prevents lateral movement from initial breach points
- Protects against advanced persistent threats (APTs)

### Business Benefits

**Regulatory Compliance**
- Supports compliance with standards like SOC 2, PCI DSS, HIPAA, and GDPR
- Provides comprehensive audit trails required by regulations
- Demonstrates due diligence in security controls implementation

**Reduced Security Incidents**
- Proactive security posture reduces likelihood of successful attacks
- Faster containment limits business impact of security incidents
- Improved security metrics demonstrate ROI of security investments

**Operational Efficiency**
- Automated security controls reduce manual intervention requirements
- Consistent security policies across hybrid and multi-cloud environments
- Centralized policy management simplifies security operations

**Business Continuity**
- Resilient architecture maintains operations during security incidents
- Rapid recovery capabilities minimize downtime
- Disaster recovery integration ensures continuous availability

## Why Ansible for Zero-Trust Implementation?

### Automation at Scale

**Consistent Configuration Management**
```yaml
# Example: Automated firewall rule deployment across thousands of systems
- name: Configure zero-trust firewall rules
  firewalld:
    zone: dmz
    source: "{{ trusted_networks }}"
    service: ssh
    permanent: yes
    state: enabled
  notify: reload firewalld
```

**Infrastructure as Code Benefits**
- Version-controlled security configurations ensure consistency
- Peer review processes for security policy changes
- Rollback capabilities for rapid incident response
- Automated testing validates security configurations

### Scalability Advantages

**Multi-System Management**
- Deploy security policies across thousands of systems simultaneously
- Ensure consistent hardening across diverse environments
- Manage complex dependencies and sequencing automatically
- Centralized lifecycle management through Red Hat Satellite integration

**Cloud and Hybrid Support**
- Consistent security controls across on-premises and cloud environments
- Support for AWS, Azure, Google Cloud, and private clouds
- Seamless integration with cloud-native security services

### Operational Benefits

**Reduced Human Error**
- Automated implementations eliminate manual configuration mistakes
- Standardized procedures ensure consistent security posture
- Validation checks prevent misconfigurations

**Faster Implementation**
- Rapid deployment of security updates and patches
- Automated compliance scanning and remediation
- Quick response to emerging threats and vulnerabilities

**Cost Effectiveness**
- Reduced operational overhead through automation
- Lower total cost of ownership compared to manual processes
- Improved resource utilization through efficient orchestration

## Zero-Trust Implementation with Ansible

### Identity and Access Management

**Multi-Factor Authentication**
```yaml
- name: Configure PAM for multi-factor authentication
  lineinfile:
    path: /etc/pam.d/sshd
    regexp: '^auth.*pam_google_authenticator.so'
    line: 'auth required pam_google_authenticator.so'
    state: present
```

**Centralized Identity Services**
```yaml
- name: Configure SSSD for centralized authentication
  template:
    src: sssd.conf.j2
    dest: /etc/sssd/sssd.conf
    mode: '0600'
  notify: restart sssd
```

### Network Security and Segmentation

**Micro-segmentation Implementation**
```yaml
- name: Implement network micro-segmentation
  firewalld:
    zone: "{{ item.zone }}"
    source: "{{ item.source }}"
    service: "{{ item.service }}"
    permanent: yes
    state: "{{ item.state }}"
  loop: "{{ microsegmentation_rules }}"
```

**TLS/SSL Configuration**
```yaml
- name: Configure TLS 1.3 minimum version
  lineinfile:
    path: /etc/ssl/openssl.cnf
    regexp: '^MinProtocol'
    line: 'MinProtocol = TLSv1.3'
```

### System Hardening

**Kernel Security Parameters**
```yaml
- name: Apply kernel hardening parameters
  sysctl:
    name: "{{ item.key }}"
    value: "{{ item.value }}"
    state: present
    reload: yes
  loop: "{{ kernel_hardening_params }}"
```

**SELinux Enforcement**
```yaml
- name: Ensure SELinux is in enforcing mode
  selinux:
    policy: targeted
    state: enforcing
```

### Continuous Monitoring and Compliance

**Audit Configuration**
```yaml
- name: Configure comprehensive auditing
  template:
    src: audit.rules.j2
    dest: /etc/audit/rules.d/zero-trust.rules
  notify: restart auditd
```

**Compliance Scanning**
```yaml
- name: Run OpenSCAP compliance scan
  shell: |
    oscap xccdf eval --profile {{ compliance_profile }} \
    --results {{ scan_results_path }} \
    {{ scap_content_path }}
  register: compliance_scan
```

### Data Protection

**Disk Encryption Setup**
```yaml
- name: Configure LUKS disk encryption
  luks_device:
    device: "{{ item.device }}"
    state: present
    passphrase: "{{ item.passphrase }}"
  loop: "{{ encrypted_devices }}"
  no_log: true
```

## Architecture Components

### Control Plane
- **Ansible Control Node**: Centralized automation and orchestration
- **Red Hat Ansible Automation Platform**: Enterprise-grade automation with RBAC
- **Optional: Red Hat Satellite**: Satellite can apply Ansible playbooks to RHEL hosts.
- **Version Control Integration**: GitOps workflows for security policies

### Target Infrastructure
- **RHEL Systems**: Servers, containers, and edge devices
- **Network Infrastructure**: Firewalls, load balancers, and switches
- **Cloud Resources**: AWS, Azure, and Google Cloud Platform resources

### Security Tools Integration
- **SIEM Platforms**: Splunk, IBM QRadar, ArcSight integration
- **Vulnerability Scanners**: OpenSCAP, Nessus, Qualys integration
- **Certificate Management**: Red Hat Certificate System, HashiCorp Vault

## Practical Implementation Roadmap

### Phase 1: Foundation (Weeks 1-4)
- [ ] Install and configure Ansible Automation Platform
- [ ] Develop baseline security playbooks
- [ ] Implement identity and access management
- [ ] Deploy basic monitoring and logging

### Phase 2: Network Security (Weeks 5-8)
- [ ] Configure perimeter firewall integration
- [ ] Implement network segmentation
- [ ] Configure firewall automation
- [ ] Deploy VPN and encrypted communications
- [ ] Integrate with network security tools

### Phase 3: Advanced Security (Weeks 9-12)
- [ ] Deploy endpoint detection and response
- [ ] Implement data loss prevention
- [ ] Configure advanced threat protection
- [ ] Integrate with threat intelligence feeds

### Phase 4: Optimization (Weeks 13-16)
- [ ] Performance tuning and optimization
- [ ] Advanced automation workflows
- [ ] Disaster recovery integration
- [ ] Continuous improvement processes

## Compliance and Standards Support

### Security Frameworks
- **NIST Cybersecurity Framework**: Complete implementation support
- **CIS Controls**: Automated implementation of critical security controls
- **ISO 27001**: Comprehensive security management system support

### Compliance Standards
- **STIG (Security Technical Implementation Guides)**: DoD security requirements
- **PCI DSS**: Payment card industry security standards
- **HIPAA**: Healthcare information security requirements
- **SOX**: Financial reporting controls and audit trails

### Government Standards
- **FedRAMP**: Federal cloud security requirements
- **FISMA**: Federal information security management
- **Common Criteria**: International security evaluation standards

## Monitoring and Metrics

### Security Metrics
- **Mean Time to Detection (MTTD)**: Average time to identify security incidents
- **Mean Time to Response (MTTR)**: Average time to respond to security events
- **False Positive Rate**: Accuracy of security monitoring systems
- **Compliance Score**: Percentage of systems meeting security standards

### Operational Metrics
- **Automation Coverage**: Percentage of security tasks automated
- **Configuration Drift**: Systems deviating from security baselines
- **Patch Compliance**: Percentage of systems with current security updates
- **Access Review Compliance**: Regular review of user access permissions

## Best Practices

### Security Configuration
1. **Defense in Depth**: Implement multiple layers of security controls
2. **Regular Updates**: Maintain current security patches and configurations
3. **Access Reviews**: Regularly review and audit user access permissions
4. **Incident Response**: Maintain and test incident response procedures

### Ansible Development
1. **Idempotency**: Ensure playbooks can run safely multiple times
2. **Error Handling**: Implement comprehensive error handling and rollback
3. **Testing**: Use molecule and other testing frameworks for validation
4. **Documentation**: Maintain clear documentation for all automation

### Operational Excellence
1. **Change Management**: Implement controlled change processes
2. **Monitoring**: Continuous monitoring of security and operational metrics
3. **Training**: Regular training on security procedures and tools
4. **Continuous Improvement**: Regular review and improvement of processes

## Getting Started

### Prerequisites
- Red Hat Enterprise Linux 8, 9, 10
- Ansible 2.9 or later (Ansible Automation Platform recommended)
- Python 3.6 or later
- Administrative access to target systems

### Quick Start
```bash
# Clone the repository
git clone <repository-url>
cd zero-trust-rhel

# Install required Ansible collections
ansible-galaxy install -r requirements.yml

# Configure inventory
cp inventory/hosts.example inventory/hosts
# Edit inventory/hosts with your system information

# Run the zero-trust baseline playbook
ansible-playbook -i inventory/hosts site.yml
```

### Configuration
1. Update `group_vars/all.yml` with your environment-specific settings
2. Configure `host_vars/` for system-specific configurations
3. Review and customize security policies in `roles/`
4. Test configurations in development environment first

## Support and Contributing

### Documentation
- [Ansible Documentation](https://docs.ansible.com/)
- [Red Hat Enterprise Linux Security Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/security_hardening/)
- [Zero Trust Architecture - NIST SP 800-207](https://csrc.nist.gov/publications/detail/sp/800-207/final)

### Community
- [Red Hat Customer Portal](https://access.redhat.com/)
- [Ansible Community](https://docs.ansible.com/ansible/latest/community/)
- [Red Hat Developer](https://developers.redhat.com/)

### Contributing
Contributions are welcome! Feel free to send me a pull request!

## Disclaimer

This automation framework provides a foundation for implementing zero-trust architecture but should be customized for your specific environment and requirements. Always test configurations in development environments before production deployment. Consult with security professionals for complex implementations and compliance requirements.

## Additional Considerations

### Image-Mode (`bootc`) for Zero Trust Hardening

Traditional package-based lifecycle management leaves room for **configuration drift** and **inconsistent patching**, which attackers exploit. RHEL 9+ introduces **image-mode** with `bootc`, allowing you to manage operating systems like containers: build once, deploy consistently, and roll forward/backward atomically.

#### Why `bootc` Strengthens Zero Trust

- **Immutable Systems**  
  Hosts are provisioned from prebuilt images, preventing unauthorized changes or "snowflake" servers.  

- **Reduced Attack Surface**  
  Only the software explicitly included in the image is present — no stray packages or services.  

- **Atomic Updates**  
  Upgrades are delivered as complete images. If something fails, rollback is instant and predictable.  

- **Fast Recovery**  
  In case of breach or corruption, redeploying a hardened RHEL image is faster and safer than patching live systems.  

- **Audit and Compliance**  
  Security teams can sign and verify system images, ensuring that only trusted, verified builds run in production.  

#### Example Workflow

1. Build a hardened RHEL image with Ansible + `bootc`:
   ```bash
   podman build -t registry.example.com/rhel-secure:latest .
   bootc container save registry.example.com/rhel-secure:latest
   ```

2. Deploy the image across your RHEL fleet:
   ```bash
   bootc switch registry.example.com/rhel-secure:latest
   ```

3. Roll back instantly if needed:
   ```bash
   bootc rollback
   ```

By combining Zero Trust principles (least privilege, strict ingress/egress controls, SELinux, OpenSCAP compliance) with **immutable, image-mode RHEL systems**, you achieve a stronger defense posture and drastically reduce the blast radius of any breach.
