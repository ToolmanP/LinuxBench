# LinuxBench

A templated Linux base system and kernel workspace designed for streamlined development and deployment.

This repository is tailored for kernel developers who value minimal overhead and full control over their development environment.

## Getting Started

### Prerequisites

This repository uses [direnv](https://direnv.net/) to automatically load environment variables and scripts, simplifying workspace management.

**Installation:**
1. Install `direnv` on your system
2. Navigate to this repository
3. Run:
   ```bash
   direnv allow
   ```

## Overview

LinuxBench supports both architecture-independent and architecture-dependent development workflows.

## Architecture Independent Development

Many Linux subsystems don't require specific hardware knowledge. For these use cases, LinuxBench leverages:
- [mkosi](https://github.com/systemd/mkosi) - Modern Linux image building
- [mkosi-kernel](https://github.com/DaanDeMeyer/mkosi-kernel) - Kernel-specific profiles

**Note:** Install these packages if you plan to use mkosi-based workflows.

### Setup Instructions

1. **Fork this repository** and add your submodules
2. **Organize your code:**
   - Place userspace packages in the `packages/` directory
   - Place the Linux kernel source in the top-level `kernel/` directory
3. **Start with the templated mkosi configuration:**
   ```bash
   cp templates/mkosi.local.conf ./mkosi.local.conf
   ```

### Shipped Profiles

Mkosi uses **profiles** to bundle configuration files under a single option. By enabling profiles and specifying source directories, mkosi builds packages in-place.

#### Example Configuration

Here's how to configure mkosi for Linux and KVM development:

```conf
[Distribution]
Distribution=fedora
Release=42

[Config]
Profiles=kernel
         containers
         network
         qemu

[Runtime]
RuntimeBuildSources=yes

[Build]
ToolsTreeRelease=42
BuildSources=kernel:kernel
             packages/qemu:qemu
```

#### Building and Running

After copying the configuration into `mkosi.local.conf`, build your system:

```bash
mkosi build -f -- -k -c configs/mkosi/kernel/6.16.y.config --qemu
```

This command will:
- Compile your selected packages
- Install them into the image at the correct locations

To boot into your custom VM:
```bash
mkosi qemu
```

This uses the default settings from `mkosi.conf`.

### Custom Profiles

You can define custom profiles for specific packages. Refer to the [mkosi manual](https://github.com/systemd/mkosi/blob/main/mkosi/resources/man/mkosi.1.md) for detailed guidance.

*New profiles are regularly added to support additional userspace packages.*

## Architecture Dependent Development

For cross-compilation workflows, LinuxBench includes infrastructure to build buildroot systems.

### Features

- **Buildroot integration** with `rootfs.defconfig` (located in `configs/`)
- **Libvirt-managed VMs** for testing
- **Automated build pipeline** for cross-compilation targets

### Usage

1. Load the environment:
   ```bash
   # .envrc is automatically loaded if direnv is set up
   ```

2. Use the buildroot control tool:
   ```bash
   br2ctl [command]
   ```

This tool helps you prepare, build, and boot into libvirt-managed VMs for development and testing.

## Additional Resources

- [mkosi Documentation](https://github.com/systemd/mkosi)
- [mkosi-kernel Profiles](https://github.com/DaanDeMeyer/mkosi-kernel)
- [mkosi Manual](https://github.com/systemd/mkosi/blob/main/mkosi/resources/man/mkosi.1.md)

## Contributing

Feel free to submit issues and pull requests to improve LinuxBench. New profiles and enhanced documentation are always welcome!
