# DC-1 (VulnHub) — Methodology-Focused Write-up

## Lab Context

- **Target:** DC-1 (VulnHub)
- **Environment:** Local, isolated lab network
- **Attacker:** Kali Linux VM
- **Scope:** Authorised, intentionally vulnerable VM

This was my first full VulnHub box. The goal was not speed or elegance, but to practice structured thinking, understand *why* each step worked, and document mistakes and learning along the way.

---

## Initial Recon

### Network Discovery

After identifying the target on the local network, basic port scanning showed a small attack surface.

**Key findings:**
- Port **80/tcp** open (HTTP)
- Web server hosting a **Drupal** site

At this stage, I deliberately avoided jumping straight to exploitation and instead focused on understanding what I was looking at.

---

## Web Enumeration

### CMS Identification

By inspecting page content and headers, the site was identified as running **Drupal 7**.

This immediately narrowed the research space:
- Drupal is well documented
- Drupal 7 has known historical vulnerabilities

Rather than blindly running tools, I paused to research what kinds of issues typically affect Drupal 7, and under what conditions they apply.

---

## Exploit Reasoning

Research led to a known **pre-authentication remote code execution** vulnerability affecting Drupal 7 installations (commonly referred to as *Drupalgeddon*).

**Key reasoning points:**
- No authentication required
- Fits the observed version
- Exploits input handling before permission checks

Because this was a lab environment designed for learning, using this exploit was appropriate and within scope.

---

## Initial Access

The vulnerability was exploited to gain a remote shell on the target system.

From there:
- A Meterpreter session was established
- A proper PTY was spawned for stability
- Initial access was confirmed as the `www-data` user

At this point, the objective shifted from exploitation to post-exploitation awareness.

---

## Local Enumeration

Once on the system, I focused on **manual enumeration** rather than automated scripts.

**Areas explored:**
- User context (`id`, groups)
- File permissions
- SUID binaries

This helped reinforce *why* privilege escalation works, rather than just running a checklist.

---

## Privilege Escalation

### SUID Enumeration

While enumerating SUID binaries, `/usr/bin/find` stood out.

This is significant because:
- `find` can execute commands
- When misconfigured with SUID, it can be abused to escalate privileges

Using `find` to execute a shell resulted in a process running with effective UID **0 (root)**.

This step was a key learning moment: the escalation was simple, but only if you understand how Linux permissions actually work.

---

## Root Access

After escalation:
- Root privileges were confirmed
- Restricted files became accessible
- The final flag was obtained

More importantly, I now understood *why* the escalation worked — not just that it did.

---

## Attack Flow

1. Network discovery  
2. Web service identification  
3. CMS version enumeration  
4. Research of applicable vulnerabilities  
5. Remote code execution  
6. Local enumeration  
7. SUID abuse (`find`)  
8. Root access  

---

## Mistakes & Learning Points

This box involved a lot of trial and error, including:
- Confusing shell contexts (`sh` vs `bash`)
- Attempting privileged actions before verifying effective UID
- Rushing commands instead of checking assumptions

These mistakes were valuable. They reinforced the importance of:
- Slowing down
- Verifying context
- Understanding what user you actually are

---

## Key Takeaways

- Enumeration matters more than exploits
- Understanding permissions beats memorising commands
- Simple misconfigurations can be more dangerous than complex vulnerabilities
- Labs are for learning — mistakes are part of the process

This box significantly improved my confidence with:
- Linux permissions
- Privilege escalation logic
- Thinking methodically instead of reactively

---

## Ethical Note

This work was carried out only against an intentionally vulnerable VM in a private lab environment.  
No systems were accessed without permission.

---

This write-up reflects my learning process as a beginner. Assistance was used where appropriate to understand concepts and unblock progress, but the focus throughout was on learning and comprehension rather than shortcutting the process.
