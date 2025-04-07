Great set of questions — these are solid for interviews or real-world troubleshooting. Here’s how you could approach each one:

---

### 1. **/var partition is 90% full. What’s your immediate action?**
- **Check disk usage:** `du -sh /var/* | sort -h`
- **Clear logs:** Rotate or delete old logs from `/var/log`
- **Clean cache:** `yum clean all` or `apt clean`
- **Archive/move:** Move large files to a different volume if needed
- **Long-term:** Extend the partition if this is recurring

---

### 2. **You’re locked out via SSH with no root access. How do you recover?**
- **Use AWS SSM Session Manager** (if agent and IAM roles are enabled)
- **Access via another user** if available
- **Mount root volume to another instance**, fix SSH config or sudoers file
- **Reboot to single-user mode** (for physical or VM access)

---

### 3. **Add 50GB to /opt using LVM without any downtime. What are the steps?**
1. **Attach disk:** `lsblk` to confirm
2. **Create PV:** `pvcreate /dev/xvdf`
3. **Extend VG:** `vgextend vg_name /dev/xvdf`
4. **Extend LV:** `lvextend -L +50G /dev/vg_name/lv_opt`
5. **Resize FS:** `resize2fs /dev/vg_name/lv_opt` or `xfs_growfs /opt`

---

### 4. **Jenkins is failing to push a Docker image to the registry. How do you troubleshoot?**
- **Check credentials:** Docker login config or Jenkins secrets
- **Check network connectivity** to registry
- **Inspect Jenkins logs**
- **Validate Docker daemon permissions** (maybe `sudo` needed)
- **Check registry policy or rate limits**

---

### 5. **Ansible playbook times out on one host out of twenty. What do you check?**
- **SSH connectivity:** `ansible host -m ping`
- **Host resource usage:** CPU/mem might be pegged
- **Ansible config:** Check forks and timeouts
- **Firewall/network rules**
- **Daemon logs on host**

---

### 6. **EC2 instance is unreachable, and it’s not a security group issue. What’s your next step?**
- **Check route tables and NACLs**
- **Instance system logs (via AWS Console)**
- **Use EC2 Serial Console or SSM**
- **Verify the instance’s public IP/Elastic IP**
- **Check CPU/Memory status (might be hung)**

---

### 7. **An S3 bucket was made public by mistake. How do you secure and audit it?**
- **Remove public access policy**
- **Enable block public access settings**
- **Audit via CloudTrail**
- **Use AWS Config for history**
- **Enable access logs going forward**

---

### 8. **RDS migration with minimal downtime — how would you approach it?**
- **Use AWS DMS** for live replication
- **Do a dry run test**
- **Switch DNS or endpoint after cutover**
- **Ensure app uses retry logic for reconnect**
- **Consider Multi-AZ failover testing**

---

### 9. **CI/CD pipeline needs rollback capability. How would you implement it?**
- **Store previous artifacts/versions**
- **Tag stable releases in Git**
- **Use Terraform with state management for infra rollback**
- **In Kubernetes:** use rollout undo
- **In Jenkins/GitHub Actions:** create manual rollback steps with version input

---

### 10. **Shell script that checks if a service is running, restarts it if not, and logs the event**
```bash
#!/bin/bash

SERVICE="nginx"
LOGFILE="/var/log/service_monitor.log"
TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')

if ! systemctl is-active --quiet $SERVICE; then
    systemctl restart $SERVICE
    echo "$TIMESTAMP - $SERVICE was down. Restarted." >> $LOGFILE
else
    echo "$TIMESTAMP - $SERVICE is running." >> $LOGFILE
fi
```

---

### 11. **Terraform script to provision an EC2 instance with a custom security group and user data**
Let me know if you want the full code block here — includes `aws_instance`, `aws_security_group`, and `user_data`.

---

### 12. **Design a highly available backend on AWS — what services and architecture would you use?**
- **ALB + Auto Scaling EC2s** (or Fargate tasks behind ALB)
- **Multi-AZ RDS or Aurora**
- **S3 for static files**
- **CloudFront for caching**
- **Route 53 for DNS failover**
- **Use VPC with private/public subnets**

---
