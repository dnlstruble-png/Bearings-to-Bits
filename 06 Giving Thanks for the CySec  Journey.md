# Holiday Week Cybersecurity Journal  
Building, Breaking, Fixing, and Finally Seeing It All Work

---

## 1. Lab Infrastructure Overhaul

This holiday week I pushed my home cyber range further than ever before. I now have a full offensive and defensive environment running simultaneously across multiple VMs.

### Current VM Fleet

<img width="471" height="257" alt="f5ca1f47-f154-445c-810d-9999545aef84" src="https://github.com/user-attachments/assets/084949c7-ca98-4c11-bef6-68d50d9f3372" />


- Kali Linux (attacker workstation)  
- Ubuntu Server running Sliver  
- Windows 10 Home Victim  
- Windows Server 2022 (Active Directory)  
- Metasploitable 2  
- Oracle Linux running Security Onion  

For the first time, I have an end-to-end lab where I can launch an attack, observe the telemetry, investigate detections, and iterate.

---

## 2. The Security Onion Saga

### The Problem

I performed three full Security Onion installs.  
Each one took about five hours, and each one broke in the same way:

- Missing directories  
- Missing configuration files  
- Sensor services not starting  
- UI reachable, but no sensor data  

I spent hours troubleshooting with no improvement.

Eventually I questioned the one thing I had not considered:

Maybe the ISO was corrupted.

### The Breakthrough

I deleted the original ISO, downloaded a new copy, and reinstalled.

This time the install only took about two hours. Everything came up correctly.  
Directories existed. Services were running. Sensor data populated exactly as expected.

When I delivered a Sliver payload and saw live telemetry appear in Security Onion, it was a major moment of validation.

<img width="1891" height="941" alt="b4ac8438-03df-4835-be8f-65d7e9de2989" src="https://github.com/user-attachments/assets/037de975-b776-4665-a6a2-24bc89fce5ea" />


### What I Learned

All the troubleshooting paid off by improving my Linux CLI skills significantly.  
Moving around directories, checking logs, managing services, and editing configs now feels more natural than it ever has.

---

## 3. Offensive Work with Sliver

Sliver has quickly become one of my favorite tools play around with. The auto-generated payload names alone give it character.

Here are the executables it generated during testing:

<img width="658" height="86" alt="f7bb3645-d121-40fe-9c26-2f48641453f7" src="https://github.com/user-attachments/assets/215a9b27-0f63-4918-be1f-e97ee7b0541f" />


- AVERAGE_PARAMEDIC.exe  
- CALM_LATHE.exe  
- CROOKED_STRUCTURE.exe  
- UNEVEN_PIG.exe  

Once the environment was stable, I successfully compiled, delivered, and executed payloads, receiving callbacks in Sliver and observing the traffic in Security Onion.

This was my first fully functioning offensive to defensive pipeline.

---

## 4. Defensive Practice

I completed the Black Hills Information Security "Getting Started in Security with MITRE ATT&CK" course.  
The labs were straightforward and great, but I intend to redo them without the walkthroughs to force deeper understanding.

I also began packet analysis training. I retain more when I take it in small, consistent chunks, so I plan to continue sprinkling it into my practice.

---

## 5. Skill Growth

### Confidence and Command Syntax

My biggest challenge continues to be remembering command syntax.  
It affects my confidence more than I want to admit.

To address this, I spent a full day building detailed cheat sheets in Obsidian.  
Using Greenshot for rapid screen captures helped me create a fast reference system for tools, commands, and workflows.

<img width="1275" height="981" alt="image" src="https://github.com/user-attachments/assets/f6749c7f-198b-4f2f-9331-82505779978f" />


This has already made me faster and more confident.

### Python Progress

I also started working with Python this week and it immediately clicked with me.  
The flow makes more sense than other languages I have tried.

Below is the working port scanner I wrote:

<img width="805" height="727" alt="73b761ac-1808-42c6-a2fb-e108a27f3b33" src="https://github.com/user-attachments/assets/013574e9-9483-46b0-89ac-50ea2537859f" />


And the output:

<img width="492" height="153" alt="61f70e6b-1a72-4c90-8307-7a8b54aed3fe" src="https://github.com/user-attachments/assets/172fc08a-e72d-4f7d-bc96-3050c12f59d7" />


My long term plan is to begin automating small tasks, create helper scripts, and eventually build toward OSCP preparation.

---

## 6. Looking Forward

My upcoming goals include:

- Getting familiar with the Security Onion UI  
- Expanding Sliver operations  
- Continuing Python development  
- Increasing packet analysis practice  
- Building full attack to detect to investigate workflows  
- Strengthening my Active Directory environment
- Learning to break the strengthened AD environment  
- Beginning OSCP prep once the fundamentals feel solid  

This holiday week felt transformative. I see real momentum building in both skill and confidence, and I finally have the environment I need to keep moving forward.

