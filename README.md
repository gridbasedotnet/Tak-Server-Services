Gridbase OpenTAKServer Health Automation Toolkit
Automated Server Monitoring • Health Validation • Email Alerts for Teams

This repository provides two automated health-monitoring scripts designed for OpenTAKServer deployments.
They perform continuous system checks, validate critical services, test port availability, and send automated email notifications.

This toolkit includes:

sanity-check – full system diagnostic

alert-check – lightweight alert-only monitor for rapid failure notifications

systemd service + timer units for automation

email integration using Gmail App Passwords + msmtp

Zero-configuration prompts (scripts ask for email + app password during setup)

These tools ensure your OpenTAKServer stays healthy for mission-critical team operations, live deployments, or distributed TAK environments.

Why This Exists

OpenTAKServer deployments involve multiple moving parts:

Core OpenTAKServer service

EUD Handler (TCP + SSL)

CoT Parser

PostgreSQL

RabbitMQ

MediaMTX

nginx reverse proxy

NetworkManager, systemd services, cloudflared tunnel, etc.

If any of these fail, clients immediately disconnect, real-time data streams drop, and teams lose situational awareness.

This toolkit gives you:

✔ Proactive monitoring
✔ Real-time email alerts
✔ Automatic logs included in notifications
✔ System health snapshots every few hours
✔ Fast detection of OpenTAKServer service failures
✔ Cloudflare Tunnel verification
✔ Port presence validation for TAK protocols
✔ Hands-off automation using systemd timers

Perfect for Gridbase, emergency comms, field deployments, long-running TAK servers, and remote-monitoring scenarios.

Included Tools
1. sanity-check

Runs every 480 minutes (8 hours) by default.

Performs:

Internet connectivity test

Cloudflare Tunnel status check

All running system services

Failed units

Critical port health

Full email report

Persistent server log

Used for scheduled health reports for IT teams.

2. alert-check

Runs every 10 minutes by default.

Triggers an immediate email alert if:

Any monitored service is NOT active (running)

Any critical port is CLOSED

Cloudflare Tunnel is down

Internet is down

The alert email includes:

The failure

Last 20 lines of journal logs

Instructions to contact IT (info@gridbase.net)

Used for real-time alerts & early warning.

Requirements

The setup script automatically installs:

msmtp (SMTP email sender)

mailutils

systemd (native)

curl, ss, and other standard Linux tools

Works on:

Ubuntu 20.04+

Debian 11+

Raspberry Pi OS

ARM64 cloud servers

Headless OpenTAKServer nodes

Gmail App Password (Required for Email Alerts)

To securely send emails, Gmail requires an App Password.

How to create it:

Visit:
https://myaccount.google.com/apppasswords

Select Mail

Select Other, name it:
Gridbase Server Monitor

Google will generate a 16-character password (example):

abcd efgh ijkl mnop


You will enter this into the script when prompted.

🔒 App Passwords bypass 2FA securely — DO NOT use your normal Gmail password.

Installation

Clone your repository:

git clone https://github.com/YOURNAME/opentakserver-health.git
cd opentakserver-health


Make both scripts executable:

sudo chmod +x sanity-check
sudo chmod +x alert-check

Configure Sanity Check

Install and enable the systemd units:

sudo cp sanity-check /usr/local/bin/
sudo cp sanity-check.service /etc/systemd/system/
sudo cp sanity-check.timer /etc/systemd/system/

sudo systemctl daemon-reload
sudo systemctl enable --now sanity-check.timer


Run manually:

sudo sanity-check

Configure Alert Check

Install and enable:

sudo cp alert-check /usr/local/bin/
sudo cp alert-check.service /etc/systemd/system/
sudo cp alert-check.timer /etc/systemd/system/

sudo systemctl daemon-reload
sudo systemctl enable --now alert-check.timer


Run manually:

sudo alert-check

Where Logs Go
Component	Location
Full sanity-check history	/var/log/sanity-check.log
Per-run temp file	/tmp/sanity-check-XXXXXX
Alert-only logs	/tmp/alert-check-XXXXXX
msmtp errors	/var/log/msmtp.log
systemd logs	journalctl -u sanity-check
Ports Monitored

These are core OpenTAKServer + TAK ecosystem ports:

Port	Use
8080	MARTI HTTP
8081	Listener
8088	EUD TCP Streaming
8089	EUD SSL Streaming
8443	MARTI HTTPS
8554	MediaMTX RTSP
9997	MediaMTX API
5432	PostgreSQL
5672	RabbitMQ
15672	RabbitMQ Web UI
80/443	nginx reverse proxy
22	SSH
64738	Mumble VOIP

If any of these fail, alert-check will email you.

Email Recipients

During setup, scripts will prompt:

Enter alert email destination:


You can provide:

Your personal email

A shared team inbox (ex: ops@company.com)

A mobile carrier SMS gateway

A distribution group for Gridbase teams

Recommended Use Cases

✔ Multi-site OpenTAKServer deployments
✔ Remote field servers (Starlink, LTE hotspots, tactical networks)
✔ Emergency operations centers
✔ Gridbase hardware customers
✔ Department and team health dashboards
✔ Automated incident detection
✔ Historical uptime tracking
✔ Real-time alerts for service outages
