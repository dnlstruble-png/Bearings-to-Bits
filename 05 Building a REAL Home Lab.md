# Home Lab Journal
### Getting My Hands Dirty With Sliver

This past weekend, I finally did what I should have done a long time ago. I stopped watching other people build labs, stopped treating tools like mysterious artifacts, and started building my own threat hunting playground one component at a time. I have played with virtual machines before, fired up Kali, and captured packets. Most of that experience was observational. I wanted to take the next step.

---

## First Steps Into The Lab

I built a small but functional setup using VMware. Nothing advanced yet, just two machines to get the fundamentals right.

**Systems**
- **Ubuntu Server** running the `sliver-server`
- **Windows 10 workstation** acting as the target

**Networking**
- Both VMs are on a **host-only network**
- Ubuntu also has a **NAT adapter** for package updates

I originally tried using VirtualBox, but it kept assigning random IP addresses, sometimes even APIPA. I did not want to burn a whole weekend troubleshooting that, so I moved the same configuration to VMware and the issue disappeared. I will still go back and figure out the VirtualBox problem later. A problem ignored is still a problem.

---

## Why I Chose Sliver

On my way home from work Friday, I was listening to a **Command and Convo** livestream on the Active Countermeasures YouTube channel. Faan Rossouw broke down a home lab design using **Sliver C2**. As he talked through the setup, something clicked. It reinforced the fact that I need to stop treating cybersecurity as a set of abstract concepts and start engaging with the tools directly.

By the time I pulled into the driveway, I had already decided what my weekend was going to look like.

---

## Struggling Forward

Sliver was brand new to me, so I spent a lot of time inside help menus. `--help` quickly became muscle memory. I broke things, fixed them, fumbled around in nano figuring out the proper structure, and repeated that process until it started feeling comfortable.

It felt less like frustration and more like a learning loop. Each hour I got faster and stopped feeling like I was staring into the unknown. Commands began to feel like structured sentences.

---

## Breakthroughs

The best moment was seeing my first beacon connect and establishing a session. After that, I wrote a file from Sliver to the Windows target. That moment got a loud celebration from me, even if it would be seen as trivial by seasoned penetration testers.

Getting hands-on removed something in my head. It taught me that reading is not enough. I need to break things, patch them, and make the machine obey. That is the difference between knowing a tool and learning one.

---

## What Comes Next

I am going to keep working this lab from both angles, offensive and defensive. Next steps include:

- Running the `sliver-client` from a separate VM, not the same machine as the server
- Adding a monitoring or detection-focused VM
- Possibly building a separate hardware box on a different network, as suggested in the livestream
- Continuing through **Antisyphon Training**
  - SOC Core Skills  
  - Getting Started in Security with BHIS and MITRE ATT&CK
- Reading through the updated **Security+** and **SecurityX** guides to reinforce fundamentals

This first step felt like opening a door into a bigger world. All I had to do was walk through it.

---
