# Field Deployment Engineer (FDE) — Responsibilities Reference

**Version:** 1.0
**Last Updated:** 2026-10-20
**Tags:** #field-engineering #deployment #FDE #career-reference

---

## 1. Role Overview

A **Field Deployment Engineer** is the bridge between product engineering and real-world customer operations. They own the end-to-end deployment of hardware and software systems at customer sites — from initial site assessment through installation, commissioning, handoff, and ongoing support. The FDE operates where "the rubber meets the road": in the field, under real-world constraints, often with an operator or customer waiting.

**Core mandate:** Deliver working systems at customer sites, on time and safely, and feed field learnings back to engineering so the product gets better with every deployment.

**What this role is NOT:**
- Not a pure developer (you write scripts/configs, not product features)
- Not a pure technician (you make engineering decisions, not just follow steps)
- Not a pure project manager (you execute hands-on, not just coordinate)

The FDE is a **hybrid role** requiring simultaneous competency in mechanical, electrical, software, and networking domains — plus the soft skills to represent the company on a customer's site, independently.

---

## 2. Core Responsibility Pillars

The role decomposes into six pillars:

| # | Pillar | One-Line Summary |
|---|--------|-----------------|
| A | Pre-Deployment Planning & Site Assessment | Know what you're walking into before you arrive |
| B | Hardware Installation & Integration | Physical systems done right the first time |
| C | Software Deployment & Configuration | Make the software work in the real environment |
| D | Commissioning, Validation & Go-Live | Prove it works end-to-end before handoff |
| E | Troubleshooting, Support & Sustainment | Keep it running when things break |
| F | Documentation, Knowledge Transfer & Enablement | Make the next deployment easier than the last |

---

## 3. Detailed Task Breakdown by Pillar

### Pillar A: Pre-Deployment Planning & Site Assessment

<details>
<summary><b>18 core tasks</b> (click to expand)</summary>

- [ ] Conduct virtual or physical site surveys before deployment to understand the environment
- [ ] Map the physical layout: equipment locations, cable routing paths, mounting surfaces, power sources
- [ ] Identify site prerequisites: power availability (voltage, amperage, phase), network connectivity, structural mounting points, environmental conditions (temperature, dust, moisture, vibration)
- [ ] Assess network infrastructure: existing LAN/WAN, firewall rules, VLAN configurations, wireless coverage, bandwidth constraints
- [ ] Identify and document site-specific constraints: restricted access hours, safety protocols, noise/heat limitations, equipment downtime windows
- [ ] Coordinate with customer stakeholders on logistics: badge/access requests, shipping/receiving of equipment, staging areas, parking/loading dock access
- [ ] Review and validate the deployment plan/SOW against actual site conditions
- [ ] Create a detailed deployment schedule with milestones, dependencies, and contingency buffers
- [ ] Identify risks and develop mitigation plans (equipment failure, site not ready, weather, parts delays)
- [ ] Order and verify all hardware/software components needed for deployment; check against BOM
- [ ] Prepare and test the deployment toolkit: hand tools, power tools, cable testers, laptops, dongles, specialized equipment
- [ ] Pre-configure hardware and software in lab/staging environment before shipping to site ("burn-in" or "end-of-line" testing)
- [ ] Capture and archive baseline configuration data for rollback capability
- [ ] Coordinate shipping logistics: crate packing, customs documentation (for international), freight scheduling, insurance
- [ ] Align with sales/project management on customer expectations, success criteria, and acceptance milestones
- [ ] Participate in pre-deployment kickoffs with internal teams (engineering, PM, support)
- [ ] Verify that all safety documentation, certifications, and compliance requirements are met before shipping
- [ ] Prepare customer-facing deployment documentation: schedules, contact lists, site rules, escalation paths

</details>

---

### Pillar B: Hardware Installation & Integration

<details>
<summary><b>20 core tasks</b> (click to expand)</summary>

- [ ] Unpack, inventory, and inspect all hardware upon arrival; document any shipping damage
- [ ] Perform mechanical installation: mount compute units, sensors, cameras, antennas, controllers, and peripheral devices
- [ ] Fabricate or modify mounting brackets, fixtures, and hardware when site conditions require custom solutions (drill, tap, cut, bend)
- [ ] Route, secure, and protect cable harnesses; ensure proper strain relief and service loops
- [ ] Terminate electrical connections: power wiring, signal wiring, ground connections, connector mating
- [ ] Install and terminate network cabling: Ethernet (Cat5e/6/6a), fiber optic, serial/RS-232/RS-485, CAN bus
- [ ] Install power distribution: DC power supplies, UPS units, circuit breakers, fuses, surge protection
- [ ] Connect sensors: LiDAR, cameras, IMUs, GPS/GNSS, encoders, proximity sensors, temperature/humidity sensors
- [ ] Integrate with existing machine/vehicle/infrastructure interfaces: CAN, J1939, Modbus, BACnet, proprietary protocols
- [ ] Install grounding and bonding systems per electrical codes and manufacturer specs
- [ ] Perform initial power-on sequences with proper current limiting and monitoring
- [ ] Verify polarity, voltage levels, and signal integrity at all connection points
- [ ] Validate safety circuits: E-Stops, safety relays, interlocks, light curtains, lockout/tagout procedures
- [ ] Document as-built wiring diagrams and installation photos for handoff
- [ ] Secure all enclosures against environmental ingress (IP rating compliance: dust, water)
- [ ] Label all cables, connectors, and components per naming conventions
- [ ] Identify and mitigate install-related failure modes: vibration loosening, thermal expansion, abrasion, UV exposure, chemical exposure
- [ ] Perform hardware acceptance checks: visual inspection, continuity tests, insulation resistance tests, ground fault verification
- [ ] Clean the installation area and remove debris/packaging per site rules
- [ ] Propose hardware design improvements based on field installation experience (mounting, harnessing, serviceability)

</details>

---

### Pillar C: Software Deployment & Configuration

<details>
<summary><b>18 core tasks</b> (click to expand)</summary>

- [ ] Install and configure operating systems on edge compute devices (typically Linux: Ubuntu, Debian, Rocky, or embedded distros)
- [ ] Install and configure application software, services, and middleware per deployment specifications
- [ ] Deploy containerized workloads (Docker, Docker Compose, Kubernetes/K3s) and manage container images/registries
- [ ] Configure networking: IP addressing (static/dynamic), DNS, DHCP, routing, VLANs, firewall rules (iptables/nftables/ufw)
- [ ] Establish and troubleshoot network connectivity between all system components: sensors ↔ compute ↔ cloud/backend
- [ ] Configure wireless links: Wi-Fi, cellular (4G/5G), radio/mesh networks, satellite links
- [ ] Parameterize software for the specific operational context: site-specific configs, calibration data, mapping files, mission parameters
- [ ] Set up VPN or secure tunnel connections to corporate/backend systems
- [ ] Configure time synchronization (NTP/PTP) across all system nodes
- [ ] Set up logging, monitoring, and telemetry collection agents (Prometheus, Grafana, ELK, custom agents)
- [ ] Configure remote access capabilities for ongoing support (SSH, remote desktop, VPN)
- [ ] Manage software licenses, certificates, and credentials securely
- [ ] Deploy and configure update mechanisms: OTA (over-the-air) update pipelines, package repositories, image registries
- [ ] Perform software version management: tag deployments, maintain version records, handle rollback procedures
- [ ] Write and execute shell scripts or Python scripts to automate repetitive configuration tasks
- [ ] Validate system security posture: disable unnecessary services, apply patches, harden configurations
- [ ] Integrate with customer IT systems: Active Directory, LDAP, SIEM, ticketing systems, cloud platforms
- [ ] Document all software configurations, versions, and customizations applied at the site

</details>

---

### Pillar D: Commissioning, Validation & Go-Live

<details>
<summary><b>15 core tasks</b> (click to expand)</summary>

- [ ] Execute end-to-end system bring-up: power-on all subsystems in correct sequence
- [ ] Verify communication between all hardware and software components (bus checks, ping tests, data flow validation)
- [ ] Perform functional testing of each subsystem individually (sensor calibration, actuator testing, compute health checks)
- [ ] Run integration tests across subsystems to validate full system behavior
- [ ] Validate safety systems under simulated fault conditions: E-Stop triggers, emergency shutdowns, watchdog timers
- [ ] Conduct performance benchmarking: uptime, throughput, latency, accuracy, intervention rates
- [ ] Test under operational conditions: real-world loads, environmental extremes, network degradation scenarios
- [ ] Execute acceptance test procedures (ATP) per customer/SOW requirements
- [ ] Capture baseline performance metrics for future reference and SLA tracking
- [ ] Identify and resolve punch list items before formal handoff
- [ ] Conduct customer acceptance walkthrough: demonstrate system functionality against success criteria
- [ ] Obtain formal sign-off from customer stakeholders on deployment completion
- [ ] Transfer system credentials, documentation, and access to customer operations team
- [ ] Establish monitoring dashboards and alerting for operational visibility
- [ ] Set expectations for hypercare/support period: response times, escalation contacts, known limitations

</details>

---

### Pillar E: Troubleshooting, Support & Sustainment

<details>
<summary><b>20 core tasks</b> (click to expand)</summary>

- [ ] Act as the primary on-site technical escalation point during and after deployment
- [ ] Diagnose hardware failures: sensor malfunctions, compute hardware faults, power issues, cable/connector failures
- [ ] Diagnose software failures: application crashes, service failures, configuration errors, version incompatibilities
- [ ] Diagnose network failures: connectivity loss, intermittent links, packet loss, DNS failures, firewall misconfigurations
- [ ] Collect and analyze logs, telemetry, and diagnostic data from all system components
- [ ] Reproduce intermittent or field-reported issues under controlled conditions
- [ ] Perform root cause analysis (RCA) on failures and document findings
- [ ] Execute workarounds and hotfixes in the field while permanent fixes are developed
- [ ] Replace or repair failed hardware components (sensors, cables, compute modules, power supplies)
- [ ] Reconfigure software or network settings to resolve operational issues
- [ ] Prioritize issues based on customer impact and safety implications
- [ ] Communicate issue status, ETAs, and workarounds to customer stakeholders clearly and regularly
- [ ] Escalate unresolved issues to internal engineering teams with complete diagnostic packages (logs, configs, reproduction steps)
- [ ] Provide remote support to customer sites between on-site deployments
- [ ] Track and manage field issues in ticketing systems; ensure timely resolution
- [ ] Monitor deployment health metrics (uptime, performance, error rates) and proactively address degradation
- [ ] Conduct post-incident reviews with internal teams; contribute to knowledge base articles
- [ ] Identify recurring field issues and advocate for product improvements with engineering
- [ ] Perform preventive maintenance during scheduled visits: firmware updates, hardware inspections, calibration checks
- [ ] Support proof-of-concept (POC) and pilot deployments: setup, data collection, evaluation, customer debrief

</details>

---

### Pillar F: Documentation, Knowledge Transfer & Enablement

<details>
<summary><b>16 core tasks</b> (click to expand)</summary>

- [ ] Create and maintain site-specific deployment documentation: as-built diagrams, configuration records, credential inventories
- [ ] Author and update standardized installation guides, SOPs, and playbooks reusable across sites
- [ ] Develop and maintain checklists: pre-deployment, installation, commissioning, handoff, maintenance
- [ ] Create wiring diagrams, cable schedules, and electrical schematics (or mark up existing ones)
- [ ] Document network architecture: topology diagrams, IP address plans, VLAN mappings, firewall rules
- [ ] Produce training materials for customer operators: user guides, quick-start cards, video walkthroughs
- [ ] Deliver hands-on training to customer operations, maintenance, and IT teams
- [ ] Enable dealer/partner/partner-technician teams to perform installations independently
- [ ] Capture and share field lessons learned with internal engineering and deployment teams
- [ ] Contribute to product feedback: file bug reports, feature requests, and design improvement suggestions based on field experience
- [ ] Maintain deployment templates and frameworks that scale across new sites
- [ ] Create and refine test protocols and acceptance criteria for repeatable commissioning
- [ ] Document site-specific customer requirements, preferences, and operational procedures
- [ ] Build and maintain FAQ / troubleshooting guides for common field issues
- [ ] Participate in internal knowledge-sharing sessions (brown-bags, retrospectives, post-mortems)
- [ ] Review and improve deployment processes based on cumulative experience across multiple sites

</details>

---

## 4. Day in the Life: Typical Deployment Week

### On-Site Deployment Week (Example — 5-day intensive install)

| Day | Theme | Key Activities |
|-----|-------|---------------|
| **Mon** | Arrival & Site Setup | Travel to site; check in with customer POC; badge/access setup; unload and inventory equipment; set up staging/work area; review deployment plan with customer; site walkthrough; confirm power/network readiness |
| **Tue** | Hardware Installation Day 1 | Mount compute units, sensors, and enclosures; run cable routes; terminate electrical connections; begin wiring harness installation; coordinate with customer facilities/electricians as needed |
| **Wed** | Hardware Installation Day 2 | Complete remaining hardware installs; terminate network cables; install power distribution; perform continuity and voltage checks; label all cables and components; photo-document the install |
| **Thu** | Software Deployment & Commissioning | Power-on and boot all systems; install/configure software; configure networking and connectivity; integrate with customer systems; execute functional tests; run end-to-end commissioning procedures; resolve issues found during testing |
| **Fri** | Validation, Training & Handoff | Complete acceptance testing; resolve punch list items; deliver operator training; hand over documentation and credentials; customer acceptance walkthrough and sign-off; pack up staging area; post-deployment status report to internal team |

### Remote Support Week (Between Deployments)

| Day | Theme | Key Activities |
|-----|-------|---------------|
| **Mon** | Issue Triage & Planning | Review open tickets and customer issues; prioritize; follow up on escalations from previous week; plan next deployment logistics |
| **Tue** | Remote Support & Engineering Interface | Provide remote troubleshooting to customer sites; file bugs and feature requests with engineering; review deployment playbook updates |
| **Wed** | Documentation & Tooling | Update deployment docs and checklists; pre-configure hardware for next deployment in lab; write automation scripts |
| **Thu** | Meetings & Knowledge Sharing | Internal syncs with engineering on field feedback; cross-team deployment reviews; training/meetings with customer success or sales |
| **Fri** | Prep & Wrap-Up | Finalize next deployment packing list; update personal notes/lessons learned; catch up on tickets; prepare travel logistics |

---

## 5. Skills & Tools Matrix

### Technical Skills

| Domain | Skills | Tools & Technologies |
|--------|--------|---------------------|
| **Mechanical** | Mounting & bracket fabrication; Cable routing & strain relief; Torque specifications; Enclosure assembly & sealing; Basic machining (drill, tap, cut) | Hand tools, Power tools (drill, impact driver, grinder), Cable ties, Conduit & flex, Torque wrenches, Crimpers & strippers |
| **Electrical** | Wiring & harness termination; Power system design & installation; Safety circuit validation; Electrical code awareness (NEC/IEC); Sensor interfacing; Grounding & bonding | Multimeter, Oscilloscope, Wire strippers/crimpers, Terminal blocks, Fuses/relays, E-Stop components, Power supplies, Signal generators |
| **Software/OS** | Linux system administration; Shell scripting (Bash); Python scripting; Containerization (Docker); Service management (systemd); Package management; Log analysis; Basic CI/CD understanding | Ubuntu/Debian/Rocky, Docker/Kubernetes, Bash, Python, Git, SSH, systemd, journalctl, top/htop, jq, grep/sed/awk |
| **Networking** | IP addressing & subnetting; VLAN configuration; Routing basics; Firewall rules (iptables/nftables); DNS/DHCP; Wireless networking (Wi-Fi, cellular, radio); VPN & secure tunnels; Packet analysis; Network troubleshooting | Cisco/Juniper CLI, ip/ifconfig, ping/traceroute, tcpdump/Wireshark, nmap, netcat, OpenVPN/WireGuard, Wi-Fi analyzers, Cellular modems |
| **Safety & Compliance** | Lockout/Tagout (LOTO); E-Stop system validation; Risk assessment; PPE requirements; Site safety protocols; Regulatory awareness (OSHA, CE, UL) | LOTO equipment, Multimeters, Safety relay testers, PPE (hard hat, gloves, safety glasses, hearing protection) |
| **Monitoring & Observability** | System health monitoring; Log aggregation; Alert configuration; Performance benchmarking; Telemetry collection | Prometheus, Grafana, ELK Stack, Datadog, custom dashboards, uptime tools |

### Soft Skills

| Skill | Why It Matters |
|-------|---------------|
| **Communication** | Translate complex technical concepts for non-technical stakeholders; explain issues clearly under pressure |
| **Autonomy & Self-Direction** | Make independent technical decisions on-site without engineering support |
| **Customer Empathy** | Understand customer pain points; manage expectations; build trust through competence |
| **Adaptability** | Handle unexpected site conditions, changing requirements, and ambiguous situations |
| **Problem-Solving Under Pressure** | Debug critical issues with operators/customers waiting; stay methodical when systems are down |
| **Teaching & Enablement** | Train customer teams with varying technical backgrounds; create clear documentation |
| **Cross-Functional Collaboration** | Work effectively with engineering, sales, PM, support, and customer teams simultaneously |
| **Attention to Detail** | One miswired connection or misconfigured IP can bring down the entire system |
| **Time & Project Management** | Manage deployment schedules, coordinate logistics, meet deadlines across multiple sites |
| **Written Documentation** | Produce clear, actionable docs that someone else can follow without you present |

---

## 6. Cross-Functional Interfaces

### Internal Stakeholders

| Team | Interaction Pattern | Topics |
|------|-------------------|--------|
| **Product Engineering** | Continuous feedback loop | Bug reports, feature requests, design improvements, build/debug support |
| **Project Management** | Per-deployment coordination | Schedules, milestones, scope, risk management |
| **Sales / Business Development** | Pre-sales & deal support | Technical validation, POC support, RFP/RFI responses, site feasibility |
| **Customer Success / Support** | Ongoing account support | Handoffs, escalation paths, customer satisfaction |
| **Hardware Engineering** | Per-deployment & design feedback | Hardware issues, installation feedback, design for manufacturability |
| **Manufacturing / QA** | Pre-deployment staging | End-of-line testing, burn-in, shipping preparation |
| **Leadership** | Periodic reporting | Deployment status, field trends, resource needs, strategic input |

### External Stakeholders

| Stakeholder | Interaction Pattern | Topics |
|-------------|-------------------|--------|
| **Customer Technical Team** | Daily during deployment | Installation coordination, technical decisions, acceptance testing |
| **Customer Operations Team** | During training & handoff | Training, SOPs, operational procedures |
| **Customer IT Team** | During setup & integration | Network access, security review, credential management |
| **Customer Leadership** | Kickoff & sign-off | Timeline, milestones, expectations, acceptance |
| **Contractors / Installers** | Per-deployment | Supervision, quality checks, coordination |
| **Dealers / Partners** | Enablement & support | Training, documentation, independent install capability |

---

## 7. Deployment Lifecycle Phases

```
Phase 0        Phase 1        Phase 2        Phase 3        Phase 4        Phase 5        Phase 6
Pre-Sales  →   Site       →   Logistics  →   On-Site    →   Commission→   Handoff   →   Sustain-
& POC          Survey &        & Staging     Installation   & Validation   & Training     ment &
Support        Planning                                     & Go-Live      & Hypercare    Support
```

| Phase | FDE Role | Key Outputs |
|-------|----------|-------------|
| **0 — Pre-Sales & POC** | Technical advisor; site feasibility assessment; POC setup and validation | POC results, technical proposal inputs, feasibility assessment |
| **1 — Site Survey & Planning** | Site assessment; deployment plan creation; risk identification | Site survey report, deployment plan, risk register, BOM validation |
| **2 — Logistics & Staging** | Pre-configure and test systems in lab; prepare shipping | Staged and tested hardware, pre-configured software, shipping docs |
| **3 — On-Site Installation** | Physical installation of all hardware and initial software deployment | Installed system, as-built documentation, cable schedules |
| **4 — Commissioning & Validation** | End-to-end testing; performance validation; safety verification | Test results, acceptance documentation, performance baselines |
| **5 — Handoff & Training** | Customer training; documentation handover; formal sign-off | Signed acceptance, trained operators, complete documentation package |
| **6 — Sustaining Support** | Remote support; preventive maintenance; continuous improvement | Resolved issues, updated docs, product feedback, improved playbooks |

---

## 8. Deployment Context Variations

> The core responsibilities above apply universally. However, the emphasis shifts based on deployment context:

### Greenfield vs. Brownfield

| Aspect | Greenfield (New Site) | Brownfield (Existing Site) |
|--------|----------------------|---------------------------|
| Planning intensity | Full site survey from scratch | Must work within existing infrastructure constraints |
| Integration complexity | Lower — build to spec | Higher — integrate with legacy systems, unknown variables |
| Customer coordination | Moderate | High — negotiate downtime windows, existing team buy-in |
| Risk profile | More predictable | More variable — hidden conditions common |

### Single-System vs. Multi-System Integration

| Aspect | Single System | Multi-System Integration |
|--------|--------------|-------------------------|
| Scope | One product deployed | Multiple products/components working together |
| Testing | Isolated functional testing | Cross-system integration testing, data flow validation |
| Troubleshooting | Contained | Must isolate issues across system boundaries |
| Customer interfaces | One POC | Multiple customer teams and IT systems |

### Customer-Operated vs. Contractor-Installed

| Aspect | Customer Operates | Contractor Installs |
|--------|------------------|-------------------|
| Training depth | FDE trains customer team thoroughly | FDE trains contractors; contractors deliver to customer |
| Quality control | FDE directly responsible | FDE supervises and validates contractor work |
| Long-term support | Direct to customer | Through contractor or both |

### Regulated vs. Non-Regulated Environments

| Aspect | Non-Regulated | Regulated (Safety-Critical, Defense, Medical, etc.) |
|--------|--------------|------------------------------------------------------|
| Documentation | Standard SOPs | Formal procedures, traceability, change control |
| Validation | Functional testing | Formal V&V, audit trails, compliance evidence |
| Safety requirements | Basic site safety | Formal safety cases, hazard analysis, redundancy |
| Deployment pace | Agile, iterate | Gate reviews, formal sign-offs, extensive testing |

---

## 9. Career Progression Framework

| Level | Scope | Autonomy | Typical Travel | Key Differentiators |
|-------|-------|----------|---------------|---------------------|
| **Junior FDE** (0-2 yrs) | Assists on deployments; executes defined tasks | Low — follows guidance from senior FDE | 30-50% | Learns the product and processes; builds foundational field skills; shadowing and supervised execution |
| **FDE** (2-4 yrs) | Owns end-to-end deployments independently | Medium — makes most technical decisions on-site | 50-70% | Independent deployment ownership; handles standard troubleshooting; represents company to customer |
| **Senior FDE** (4-7 yrs) | Leads complex, multi-site, or first-of-kind deployments | High — architecture decisions, novel solutions | 50-70% | Mentors junior FDEs; authors playbooks; interfaces directly with engineering on product direction; handles escalated issues |
| **Lead / Principal FDE** (7+ yrs) | Program-level ownership; shapes deployment strategy | Very High — sets standards and methodology | 30-50% | Defines deployment framework; drives tooling/process improvements; cross-functional leadership; industry representation |

### Skill Progression Trajectory

```
Junior FDE                          Senior FDE                        Principal FDE
    │                                   │                                  │
    ├─ Execute installation steps       ├─ Design installation approach   ├─ Define deployment standards
    ├─ Follow existing playbooks        ├─ Author new playbooks           ├─ Build deployment framework
    ├─ Escalate issues to engineering   ├─ Diagnose root cause in field   ├─ Predict failure modes pre-deployment
    ├─ Learn one product deeply         ├─ Master product suite           ├─ Shape product requirements from field
    └─ Follow customer training plans   ├─ Create training programs       └─ Set company-wide FDE strategy
                                        └─ Mentor junior FDEs
```

---

## 9. Industry Context Notes

> The FDE role spans industries. While the core is the same, the flavor changes:

| Industry | Typical Systems | Unique Aspects |
|----------|----------------|----------------|
| **Robotics / Autonomy** | Robotic arms, AMRs, unmanned vehicles, edge compute | ROS 2, safety-certified systems, physical interaction with machinery |
| **Defense / Aerospace** | Sensors, C2 systems, ruggedized compute | Security clearances, austere environments, MIL-SPEC hardware, radio/mesh networking |
| **Energy / Utilities** | Solar inverters, grid systems, SCADA | Regulatory compliance, high-voltage safety, utility interconnection standards |
| **Mining / Construction** | Autonomy on heavy equipment, telemetry | Extreme environments (dust, heat, vibration), heavy machinery safety, remote sites |
| **Public Safety** | Drone detection, video systems, command center tech | Law enforcement coordination, RF/airspace regulations, real-time operations |
| **Industrial Automation** | PLCs, robotic cells, sensors, HMIs | OT networking (Modbus, PROFINET), industrial safety standards, factory environments |
| **Telecom / Network** | Base stations, network equipment, fiber | RF knowledge, tower climbing, telecom standards, high-volume rollout |

---

## 10. Personal Reflection / Customization

> Use this section to tailor the reference to your own context.

### My Current Focus Areas
- [ ] Area 1: ________________________________
- [ ] Area 2: ________________________________
- [ ] Area 3: ________________________________

### Skill Gaps to Address
| Skill | Current Level | Target Level | Action Plan |
|-------|--------------|--------------|-------------|
| | | | |
| | | | |
| | | | |

### Key Lessons Learned (Field Notes)
| Date | Site/Situation | Lesson | Action Taken |
|------|---------------|--------|--------------|
| | | | |
| | | | |

### My Deployment Checklist (Customized)
- [ ] (Add site-specific items here)

---

*This document is a living reference. Update it as you gain field experience and as the role evolves.*
