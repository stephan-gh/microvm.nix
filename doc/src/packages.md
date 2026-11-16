# Running a MicroVM as a package

Quickly running a MicroVM interactively is great for testing. You get
to interact with its console.

There are drawbacks: no preparation for TAP network interfaces is done
and no virtiofsd is started. These can be worked around by relying on
9p shares and using qemu's `user` network interfaces.

## Immediately running a nixosConfiguration

To run a `nixosConfiguration` off your Flake directly use:
```bash
nix run .#nixosConfigurations.my-microvm.config.microvm.declaredRunner
```

## Add a runner package to your Flake

To add this runner permanently add a package like this to the outputs
of your `flake.nix`:
```nix
packages.x86_64-linux.my-microvm = self.nixosConfigurations.my-microvm.config.microvm.declaredRunner;
```

You can then run the MicroVM with a simple `nix run .#my-microvm`

## Pass extra hypervisor arguments

Arguments after `--` are forwarded to the hypervisor for that invocation.
For example, when debugging an early boot failure in a QEMU guest:

```bash
nix run .#my-microvm -- -s -S
```

The `-s` option opens QEMU's GDB server on TCP port 1234, and `-S` keeps
the guest paused until the debugger resumes it. This lets you attach a
debugger before the guest boots without changing the VM configuration.

These arguments are appended after the configured hypervisor arguments
and any arguments produced by `microvm.extraArgsScript`.
