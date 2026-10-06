```python
import os
import subprocess
import sys

def fix_install_script():
    script_path = "http://rustchain.org/install.sh"
    install_script = "curl -L -O -s " + script_path + "; sh -s"  # Bash shim
    script = subprocess.check_output(["curl", "-L", "-o", "-", script_path], text=True)
    install_script += script

    # Replace script paths
    install_script = install_script.replace("/usr/local/bin/", "/usr/local/sbin/")
    install_script = install_script.replace("/usr/local/bin/", "/opt/rustchain/bin/")
    install_script = install_script.replace("/usr/local/bin/", "/usr/local/bin/")

    # Fix macOS paths
    if sys.platform == "darwin":
        # Use `readlink -f` instead of `sed -i`
        install_script = install_script.replace("sed -i", "readlink -f")
        # Handle both Apple Silicon and Intel
        install_script = install_script.replace("grep -P", "grep")

    # Fail loudly on unsupported platforms
    if sys.platform not in ["darwin", "linux"]:
        print("Unsupported platform: " + sys.platform)
        sys.exit(1)

    # Run the script
    subprocess.run(["bash", install_script], check=True)

# Run the fix
fix_install_script()
```