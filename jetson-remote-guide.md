markdown---
layout: page
title: "NVIDIA Jetson Remote Access Guide"
permalink: /jetson-remote-guide/
---

# NVIDIA Jetson Remote Access & Management Guide

A comprehensive guide for remotely accessing and managing NVIDIA Jetson devices, including SSH, VNC, system monitoring, and persistent sessions.

---

## Table of Contents

1. [SSH - Remote Terminal Access](#ssh---remote-terminal-access)
2. [VNC - Remote GUI Access](#vnc---remote-gui-access)
3. [System Monitoring](#system-monitoring)
4. [Screen - Persistent Sessions](#screen---persistent-sessions)
5. [Quick Reference](#quick-reference)

---

## SSH - Remote Terminal Access

SSH (Secure Shell) allows you to remotely control your Jetson's command line from another computer over the network.

### Initial Setup on Jetson

Before you can SSH into your Jetson, ensure SSH service is installed and running:

```bash
# Install SSH server (if not already installed)
sudo apt update
sudo apt install openssh-server

# Enable SSH to start automatically on boot
sudo systemctl enable ssh

# Start SSH service
sudo systemctl start ssh

# Check SSH status
sudo systemctl status ssh
Finding Your Jetson's IP Address
You need the Jetson's IP address to connect from your desktop:
bash# Show all network interfaces and IP addresses
ip addr show

# Show only the main IP address
hostname -I

# Alternative method
ifconfig
Look for addresses starting with 192.168.x.x or 10.x.x.x (local network addresses).
Connecting from Your Desktop
Windows (Using Anaconda Terminal or PowerShell)
bash# Basic connection
ssh username@jetson_ip_address

# Example
ssh jetson_user@192.168.1.100
Creating SSH Shortcuts (Optional)
To avoid typing the IP address every time, create an SSH config file:
Windows:
bash# Edit SSH config (create if doesn't exist)
notepad C:\Users\your_username\.ssh\config
Add this content:
Host jetson
    HostName 192.168.1.100
    User jetson_user
Then connect with:
bashssh jetson
File Transfer with SCP
SCP (Secure Copy) transfers files between your desktop and Jetson over SSH.
Copy Files TO Jetson
bash# Copy single file
scp local_file.txt username@jetson_ip:~/

# Copy to specific directory
scp local_file.txt username@jetson_ip:~/Desktop/projects/

# Copy entire directory
scp -r local_directory/ username@jetson_ip:~/
Copy Files FROM Jetson
bash# Copy single file from Jetson to current directory
scp username@jetson_ip:~/remote_file.txt .

# Copy to specific local directory
scp username@jetson_ip:~/remote_file.txt C:/Users/your_name/Desktop/

# Copy entire directory
scp -r username@jetson_ip:~/remote_directory/ ./local_destination/
File Transfer Tips

Use forward slashes (/) in Windows paths for SCP
The . represents current directory
Navigate to your target directory first, then use . as destination
Use quotes around paths with spaces: "C:/My Documents/file.txt"

Safe Shutdown and Restart
Never just unplug your Jetson! Use these commands for safe shutdown:
bash# Shutdown immediately
sudo shutdown -h now

# Alternative shutdown command
sudo poweroff

# Restart the Jetson
sudo reboot

# Shutdown after delay (useful for running scripts)
sudo shutdown -h +10    # Shutdown in 10 minutes
sudo shutdown -h 20:30  # Shutdown at 8:30 PM
Basic SSH Commands
bash# Exit SSH session
exit

# Check current directory
pwd

# List files
ls -la

# Change directory
cd /path/to/directory

# Check disk space
df -h

# Check who's logged in
who
SSH Troubleshooting
Connection refused:

Check if SSH service is running: sudo systemctl status ssh
Verify IP address is correct
Check firewall settings

Permission denied:

Verify username is correct
Check if user account exists on Jetson

Network unreachable:

Ensure both devices are on same network
Try pinging the Jetson: ping jetson_ip_address


VNC - Remote GUI Access
VNC allows you to see and control the Jetson's desktop interface remotely, useful when you need graphical applications.
Setting Up VNC on Jetson
Install VNC Server
bash# Update system
sudo apt update

# Install VNC server
sudo apt install tightvncserver

# Or for better performance
sudo apt install x11vnc
Configure VNC Server
bash# Set VNC password (run as your user, not sudo)
vncpasswd

# Start VNC server (creates desktop session)
vncserver :1 -geometry 1920x1080 -depth 24

# For x11vnc (shares existing desktop)
x11vnc -display :0 -auth guess -forever -loop -noxdamage -repeat -rfbauth ~/.vnc/passwd -rfbport 5900 -shared
Enabling GUI on Headless Jetson
If your Jetson doesn't automatically start the GUI:
bash# Start display manager
sudo systemctl start gdm

# Enable GUI to start on boot
sudo systemctl enable gdm

# Check GUI status
sudo systemctl status gdm
Connecting from Desktop
Install VNC Viewer
Download VNC Viewer from RealVNC or use built-in clients:

Windows: RealVNC Viewer, TightVNC Viewer
Mac: Built-in Screen Sharing or VNC Viewer
Linux: vncviewer command

Connect to Jetson
Address: jetson_ip_address:5900
Example: 192.168.1.100:5900
Enter your VNC password when prompted.
Optimizing VNC Performance
bash# Lower color depth for faster performance
vncserver :1 -geometry 1920x1080 -depth 16

# Smaller resolution for slower networks
vncserver :1 -geometry 1366x768 -depth 24

# Kill existing VNC session
vncserver -kill :1
Disabling GUI (Headless Mode)
To free up memory and GPU resources when not using GUI:
bash# Stop GUI completely
sudo systemctl stop gdm

# Verify GUI is stopped
ps aux | grep -E "Xorg|gdm|gnome-session"
# No output means GUI is fully stopped

# Re-enable later
sudo systemctl start gdm

System Monitoring
Understanding your Jetson's performance is crucial for running multiple tasks efficiently.
htop - Interactive Process Monitor
htop provides a real-time view of system resources and running processes.
Installation and Basic Usage
bash# Install htop (if not present)
sudo apt install htop

# Run htop
htop
Understanding htop Display
CPU Section (Top):

Numbers 0-5 represent CPU cores (Jetson Orin Nano has 6 cores)
Percentage shows current usage per core
Color bars indicate: User processes (green), System processes (red), Nice processes (blue)

Memory Section:
Mem[||||||||||||     2.90G/7.44G]
     ^used           ^total available
Load Average:
Load average: 0.08 0.04 0.01
              ^1min ^5min ^15min

Values under 1.0 per core indicate good performance
Values over number of cores indicate system strain

Process List:

PID: Process ID
USER: Who owns the process
CPU%: CPU usage percentage
MEM%: Memory usage percentage
COMMAND: What program is running

htop Navigation

Arrow keys: Navigate process list
F9 or k: Kill process
F6 or <: Change sort column
F4 or \: Filter processes
F10 or q: Quit

tegrastats - Jetson-Specific Monitoring
tegrastats shows Jetson-specific information including GPU usage and temperatures.
bash# Run tegrastats
tegrastats

# Run for specific duration
tegrastats --interval 1000 --logfile stats.log
Sample tegrastats Output:
11-07-2024 15:23:45 RAM 2876/7764MB (lfb 1045x4MB) SWAP 0/3882MB (cached 0MB) 
CPU [12%@1.42,6%@1.42,4%@1.42,8%@1.42,2%@1.42,1%@1.42] 
GPU 245MHz/245MHz PLL@37C MCPU@37C PMIC@100C Tdiode@36.25C 
SOCTHERM@36C VDD_SYS_GPU_SOC 2040mW/2040mW VDD_SYS_CPU_CV 760mW/760mW 
VDD_IN 3960mW/3960mW VDD_SYS_DDR 564mW/564mW VDD_SYS_SOC 1200mW/1200mW
Key Information:

RAM: Memory usage (used/total)
CPU: Usage per core with frequencies
GPU: Current/max frequency
Temperatures: Various sensor readings
Power: Power consumption by component

Other Monitoring Commands
Memory Information
bash# Detailed memory usage
free -h

# Memory usage by process
ps aux --sort=-%mem | head

# Available memory
cat /proc/meminfo | grep Available
CPU Information
bash# CPU details
lscpu

# Current CPU frequencies
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_cur_freq

# CPU temperature
cat /sys/class/thermal/thermal_zone*/temp
Disk Usage
bash# Disk space by filesystem
df -h

# Directory sizes
du -sh /*

# Largest files/directories
du -sh * | sort -hr | head -10
Network Monitoring
bash# Network interface statistics
cat /proc/net/dev

# Active connections
ss -tuln

# Network usage (install with: sudo apt install nload)
nload
Interpreting Resource Usage
Good Performance Indicators

CPU Load: Under 6.0 (number of cores)
Memory: Under 80% usage
Temperature: Under 80°C
Swap Usage: 0 or minimal

Warning Signs

High CPU Load: Consistently above number of cores
High Memory: Above 90% with swap usage
High Temperature: Above 85°C
Many Zombie Processes: Indicates software issues


Screen - Persistent Sessions
Screen allows you to run programs that continue executing even when you disconnect from SSH. Essential for long-running tasks.
Installation
bash# Install screen
sudo apt update
sudo apt install screen
Basic Screen Usage
Creating and Managing Sessions
bash# Start new screen session
screen

# Start named session (recommended)
screen -S session_name

# Example: Start session for a Python script
screen -S data_processing
Running Commands in Screen
bash# Once inside screen, run your commands normally
python long_running_script.py

# Your script runs inside the screen session
# If connection drops, script continues running
Detaching from Screen
bash# Detach from current screen session (keeps it running)
# Press: Ctrl+A then D

# You'll see: [detached from session_name]
# Script continues running on Jetson
Reconnecting to Screen Sessions
bash# List all screen sessions
screen -ls

# Sample output:
# There are screens on:
#     12345.data_processing    (Detached)
#     12346.monitoring         (Detached)

# Reattach to specific session
screen -r session_name

# Example
screen -r data_processing

# If only one session exists
screen -r
Advanced Screen Commands
Session Management
bash# Kill specific session
screen -X -S session_name quit

# Kill current session (from inside screen)
# Press: Ctrl+A then K, then Y to confirm

# Force kill unresponsive session
screen -S session_name -X quit
Multiple Windows in Screen
bash# Create new window (from inside screen)
# Press: Ctrl+A then C

# Switch between windows
# Press: Ctrl+A then 0-9 (window numbers)

# List all windows
# Press: Ctrl+A then "

# Name current window
# Press: Ctrl+A then A, then type name
Screen Configuration
Create ~/.screenrc for custom settings:
bash# Sample .screenrc content
startup_message off
hardstatus alwayslastline
hardstatus string '%{= kG}[ %{G}%H %{g}][%= %{=kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%?%= %{g}][%{B}%Y-%m-%d %{W}%c %{g}]'
Practical Screen Workflows
Running Multiple Long Tasks
bash# Terminal 1: SSH to Jetson
ssh user@jetson_ip

# Start first task
screen -S task1
python script1.py
# Ctrl+A then D to detach

# Start second task
screen -S task2
python script2.py
# Ctrl+A then D to detach

# Start monitoring session
screen -S monitoring
htop
# Ctrl+A then D to detach

# Exit SSH - all tasks continue running
exit
Monitoring Multiple Sessions
bash# Reconnect later
ssh user@jetson_ip

# Check all sessions
screen -ls

# Quickly check each session
screen -r task1
# Check progress, then Ctrl+A D

screen -r task2
# Check progress, then Ctrl+A D

screen -r monitoring
# Check system resources, then Ctrl+A D
Screen for Development
bash# Development workflow
screen -S development

# Window 0: Code editing
nano my_script.py

# Ctrl+A C (new window)
# Window 1: Testing
python my_script.py

# Ctrl+A C (new window)  
# Window 2: Monitoring
htop

# Switch between windows with Ctrl+A 0, Ctrl+A 1, Ctrl+A 2
Screen vs tmux
While screen is covered here, tmux is an alternative with similar functionality:
bash# tmux equivalents
tmux new-session -s session_name  # Create session
tmux detach                       # Detach (or Ctrl+B D)
tmux attach-session -t session_name  # Reattach
tmux list-sessions               # List sessions
Choose based on preference - both accomplish the same goal of persistent sessions.

Quick Reference
SSH Quick Commands
bash# Connect
ssh user@jetson_ip

# File transfer
scp file.txt user@jetson_ip:~/
scp user@jetson_ip:~/file.txt .

# Safe shutdown
sudo poweroff
Screen Quick Commands
bash# Create session
screen -S name

# Detach
Ctrl+A then D

# List sessions
screen -ls

# Reattach
screen -r name

# Kill session
screen -X -S name quit
Monitoring Quick Commands
bash# System overview
htop

# Jetson-specific stats  
tegrastats

# Memory usage
free -h

# Disk usage
df -h
VNC Quick Setup
bash# Start GUI
sudo systemctl start gdm

# Stop GUI (headless)
sudo systemctl stop gdm

# Connect: jetson_ip:5900
Useful File Locations
bash# SSH config
~/.ssh/config

# Screen config
~/.screenrc

# System logs
/var/log/syslog

# Network configuration
/etc/netplan/
Emergency Commands
bash# Kill unresponsive process
sudo kill -9 PID

# Emergency restart
sudo reboot

# Check system errors
dmesg | tail

# Check service status
sudo systemctl status service_name
