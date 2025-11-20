# Beginning the ICS Threat Intelligence Practice

I have never learned anything by watching from the sidelines. I’m a tactile learner - I understand things by doing them. Instead of memorizing someone else’s workflow, I decided to build threat intelligence skills through practice.

This project is my way of learning by investigating real threats, interpreting their impact, and communicating them clearly. Today marks the first entry documenting that process.

---

### Where to Focus

I did not begin with a breach article or a prescribed workflow. I started by asking myself a simple question. Where should I focus my attention?

My first thoughts centered around the financial sector. That area is heavily covered, partly because of risk, but also because money is the golden calf of the modern era. After that, I considered medical devices. That sector should be covered because lives are at stake, but realistically, most of its attention is driven by liability and lawsuits. So once again, money.

This process led me to industrial systems. Not because they are unimportant, but because they do not always receive the same mainstream attention. If a bank goes offline, headlines explode. If a rubber plant goes offline, the public barely notices unless someone is injured. Ironically, the physical stakes are usually higher in ICS environments. Failure is not only financial. It can be mechanical, chemical, and sometimes lethal.

That made industrial control systems the best place to start.

---

### First Steps Into the Research

As part of laying a foundation, I set up RSS feeds. They will help in the long term. However, the incident I chose did not come from them. I began with a search. I looked specifically for ransomware activity affecting industrial operations and kept seeing the name LockBit appear. That began the rabbit hole.

A few articles later, I realized that the operational risk did not come from tampering with PLC logic, but from losing visibility into system behavior. At that point, I went directly to MITRE ATT&CK for ICS. I did not start with a framework. I started with a question, then a hunch. I skimmed through the techniques and found that, although the technique itself is not attributed to LockBit, MITRE ATT&CK for ICS - T0828 (Loss of View) can occur when ransomware encrypts operator visibility systems through other techniques.

I did not need MITRE to tell me why Loss of View is dangerous. I already know what it means to lose sight of a process. I have lived inside that world.

---

### Blending Published Information With Personal Experience

The information about LockBit’s behavior, its affiliates, and its initial access methods came from public reporting. However, the operational risk analysis came from what I already know.

I have worked around rotating assets, control loops, HMIs, historians, emergency shutdowns, trending requirements, and all the other systems used to keep a plant moving. When the screen goes dark, the logic does not stop being dangerous. You simply stop seeing it.

Loss of visibility leads to loss of safe control. Once that happens, you are no longer operating the plant. The safety system is operating you. That system can save lives, but it is not designed to run the process. It is designed to hit the brakes.

So this exercise was not just research. It was a merge between real operational experience and publicly available intelligence.

---

### A Surprising Shift in Perspective

This exercise also surprised me on a personal level. I have said before that OT and ICS might not be where I wanted to land within cybersecurity. I viewed it as familiar territory, almost too close to what I already know. But approaching it from the angle of OSINT and threat intelligence changed that perspective.

ICS threat research feels just as interesting as anything in finance, defense, or cloud. The difference is that I can walk into it with a deeper understanding of the consequences without having to read ten articles just to grasp the risks. Eighteen years around industrial systems gives me a direct lens into what happens when someone loses visibility into a process. I did not need someone else to explain it. I have seen what happens when people lose the ability to see what they are controlling.

That realization matters. It means I might have an edge here, not because I know more about cyber, but because I already understand the physical world it affects. That counts for something.

---

### Tools and Techniques Used

For this first run, I did not use anything special. Only a web browser and MITRE ATT&CK. That was enough to form a working hypothesis, map it to a known technique, and explain why it matters in a physical environment.

Later, I may use more structured analytic frameworks, OSINT tools, or aggregation methods. For now, the priority is analysis, not tooling.

---

### Purpose of the Project

This practice is meant to build skill, not perfection. I want to document the learning process publicly. The goal is to investigate threats that affect the physical world, understand them clearly, and communicate the risk in plain terms. If I can learn to do that consistently, the portfolio will build itself over time.

---

### Closing Thought

This first exercise reinforced something important. The danger does not come from malware that alters logic. The danger comes from losing the ability to see what the logic is doing. That is where industrial threat intelligence becomes meaningful, and that is the foundation I want to build on in the entries that follow.

---

### Link to Initial Report

https://github.com/dnlstruble-png/Bearings-to-Bits/blob/Practice-Projects/OSINT/01%20OSINT%20Report%20(Lockbit%20Initial%20Report).md

