# NVIDIA Driver Integration for Tonarchy ISO

## Changes Made

### 1. Added NVIDIA Driver Packages to XFCE_PACKAGES
**File:** `src/tonarchy.c` (Line 28)

Added the following NVIDIA-related packages:
- `nvidia` - NVIDIA driver
- `nvidia-utils` - NVIDIA utilities
- `nvidia-dkms` - NVIDIA DKMS modules
- `nvidia-settings` - NVIDIA settings utility
- `nvidia-peermem` - Peer-to-peer memory access
- `libva-mesa-driver` - Video acceleration
- `nvidia-driver-libs` - NVIDIA driver libraries

### 2. Created configure_nvidia() Function
**File:** `src/tonarchy.c` (Lines 1526-1574)

New function that:
- Configures GPU power management
- Creates `/etc/nvidia/profile.d/` directory
- Creates an auto-installation script that checks for NVIDIA driver presence
- Adds the current user to the `wheel` group for sudo access
- Displays a success message upon completion

### 3. Updated main() Function
**File:** `src/tonarchy.c` (Lines 1700-1714)

Modified to call `configure_nvidia()` after system configuration for both:
- **Beginner (XFCE) installation**
- **Oxidized (OXWM) installation**

## How It Works

### During ISO Build/Installation:
1. **Package Installation:** NVIDIA driver packages are installed via `pacstrap` along with the rest of the system packages
2. **Configuration:** The `configure_nvidia()` function runs and sets up the environment
3. **Auto-Install Profile:** Creates a script at `/etc/nvidia/profile.d/nvidia-auto-install.sh` that will run after installation to verify and enable NVIDIA drivers

### Post-Installation:
When the user boots into the installed system:
1. The auto-install script checks for NVIDIA driver libraries
2. If detected, it confirms drivers are working
3. User can verify with `nvidia-smi`
4. If not detected, it provides instructions to manually install

## Usage

### After Installing from ISO:
```bash
# Verify NVIDIA drivers are working
nvidia-smi

# Check driver version
nvidia-smi --query-gpu=driver_version,version --format=csv

# View GPU information
lspci | grep -i nvidia
```

### Troubleshooting:
If drivers don't work after installation:
```bash
# Remove and reinstall NVIDIA drivers
pacman -Rns nvidia nvidia-utils nvidia-dkms nvidia-settings
pacman -S nvidia nvidia-utils nvidia-dkms nvidia-settings nvidia-peermem libva-mesa-driver

# Reboot and check
nvidia-smi
```

## Notes
- NVIDIA drivers are installed for the XFCE (Beginner) installation level
- The OXWM level also gets NVIDIA drivers (added via package list)
- DKMS support ensures drivers rebuild automatically after kernel updates
- The auto-install profile provides a fallback mechanism for manual installation
