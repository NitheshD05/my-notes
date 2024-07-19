This Script will automate Nmap scan, with dynamic Functions.
```
import subprocess
import re
import argparse
import time
from colorama import Fore, Style, init

# Initialize colorama for colored output
init(autoreset=True)

def print_hacker_vibes(message, color=Fore.CYAN):
    """Prints a message in hacker-style vibes with specified color."""
    print(f"{color}{message}{Style.RESET_ALL}")

def simulate_loading(message, delay=0.1):
    """Simulates a loading animation for a specified message."""
    chars = "/—\\|"
    for _ in range(10):
        for char in chars:
            time.sleep(delay)
            print(f"\r{message} {char}", end='', flush=True)
    print("\r" + " " * (len(message) + 5), end='', flush=True)

def run_nmap_command(command, output_file):
    """Runs an Nmap command and saves the output to a file."""
    scan_type = command[-1].split('/')[-1].split('.')[0].upper() + " Scan"
    print_hacker_vibes(f"Running {scan_type}: {' '.join(command)}", Fore.YELLOW)
    simulate_loading("Scanning...", delay=0.1)
    print()  # Move to the next line after the loading animation
    with open(output_file, 'w') as f:
        process = subprocess.Popen(command, stdout=subprocess.PIPE, stderr=subprocess.STDOUT)
        for line in process.stdout:
            line = line.decode('utf-8')
            print_hacker_vibes(line.strip(), Fore.CYAN)
            f.write(line)
        process.wait()

def extract_open_ports(nmap_output_file):
    """Extracts open ports from an Nmap output file."""
    with open(nmap_output_file, 'r') as f:
        output = f.read()
    open_ports = re.findall(r'(\d+)/tcp\s+open', output)
    return list(map(int, open_ports))

def compare_ports(original_file, new_file):
    """Compares open ports between two Nmap output files."""
    with open(original_file, 'r') as f1, open(new_file, 'r') as f2:
        original_ports = set(re.findall(r'(\d+)/tcp\s+open', f1.read()))
        new_ports = set(re.findall(r'(\d+)/tcp\s+open', f2.read()))
    return list(new_ports - original_ports)

def main():
    parser = argparse.ArgumentParser(description="🔓 Unleash the Nmap Fury! 🔓",
                                     formatter_class=argparse.ArgumentDefaultsHelpFormatter)
    parser.add_argument("target", help="Target IP address or hostname")
    parser.add_argument("-d", "--default", action="store_true", help="Run only the default scan")
    parser.add_argument("-s", "--service", action="store_true", help="Run only service and script scan")
    parser.add_argument("-a", "--all-ports", action="store_true", help="Run only all-port scan")
    parser.add_argument("--speed", action="store_true", help="Run scans in fast mode")
    parser.add_argument("--start", type=int, help="Start port for scanning")
    parser.add_argument("--end", type=int, help="End port for scanning")
    parser.add_argument("-v", "--verbose", action="store_true", help="Run scans in verbosity mode")
    args = parser.parse_args()

    target = args.target
    speed_mode = args.speed
    start_port = args.start
    end_port = args.end
    verbose = args.verbose

    dnmap_output = 'dnmap.txt'
    snmap_output = 'snmap.txt'
    anmap_output = 'anmap.txt'
    asnmap_output = 'asnmap.txt'

    # Define base nmap command without -Pn by default
    nmap_base_cmd = ['nmap', target]
    if args.default:
        nmap_base_cmd.append('-Pn')
    if verbose:
        nmap_base_cmd.append('-v')
    if speed_mode:
        nmap_base_cmd.append('-T5')

    # Define scan types
    scan_commands = {
        "default": nmap_base_cmd + ['-oN', dnmap_output],
        "service": nmap_base_cmd + ['-sC', '-sV', '-A', '-oN', snmap_output],
        "all_ports": nmap_base_cmd + ['-p-', '--open', '-oN', anmap_output]
    }

    # If port range is specified, add it to the base command
    if start_port is not None and end_port is not None:
        port_range = f"{start_port}-{end_port}"
        scan_commands["default"] = nmap_base_cmd + ['-p', port_range, '-oN', dnmap_output]
        scan_commands["service"] = nmap_base_cmd + ['-p', port_range, '-sC', '-sV', '-A', '-oN', snmap_output]

    def run_scan(scan_type):
        command = scan_commands[scan_type]
        output_file = command[-1]
        run_nmap_command(command, output_file)

    if args.default:
        print_hacker_vibes("Starting Default Nmap Scan...", Fore.MAGENTA)
        run_scan("default")
    elif args.service:
        print_hacker_vibes("Starting Service and Script Scan...", Fore.MAGENTA)
        run_scan("service")
    elif args.all_ports and (start_port is None or end_port is None):
        print_hacker_vibes("Starting Full All-Port Scan...", Fore.MAGENTA)
        run_scan("all_ports")
        
        # Compare dnmap.txt and anmap.txt to find new ports
        new_ports = compare_ports(dnmap_output, anmap_output)
        if new_ports:
            new_ports_str = ','.join(map(str, new_ports))
            print_hacker_vibes(f"New open ports found: {new_ports_str}", Fore.MAGENTA)
            print_hacker_vibes("Starting New Ports Service and Script Scan...", Fore.MAGENTA)
            command = nmap_base_cmd + ['-p', new_ports_str, '-sV', '-sC', '-oN', asnmap_output]
            run_nmap_command(command, asnmap_output)
    else:
        # If no specific scan is selected, run the necessary scans
        if args.default:
            print_hacker_vibes("Starting Default Nmap Scan...", Fore.MAGENTA)
            run_scan("default")
        else:
            print_hacker_vibes("Starting Default Nmap Scan...", Fore.MAGENTA)
            run_scan("default")

            print_hacker_vibes("Starting Service and Script Scan...", Fore.MAGENTA)
            run_scan("service")

            if start_port is None or end_port is None:
                print_hacker_vibes("Starting Full All-Port Scan...", Fore.MAGENTA)
                run_scan("all_ports")
                
                # Compare dnmap.txt and anmap.txt to find new ports
                new_ports = compare_ports(dnmap_output, anmap_output)
                if new_ports:
                    new_ports_str = ','.join(map(str, new_ports))
                    print_hacker_vibes(f"New open ports found: {new_ports_str}", Fore.MAGENTA)
                    print_hacker_vibes("Starting New Ports Service and Script Scan...", Fore.MAGENTA)
                    command = nmap_base_cmd + ['-p', new_ports_str, '-sV', '-sC', '-oN', asnmap_output]
                    run_nmap_command(command, asnmap_output)

if __name__ == "__main__":
    main()
    
```
