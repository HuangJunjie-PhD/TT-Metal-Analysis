---
project: ttsim
github: https://github.com/tenstorrent/ttsim
deepwiki: https://deepwiki.com/tenstorrent/ttsim/4-user-guide
---

# User Guide

Relevant source files
*   [README.md](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1)

This guide provides practical guidance for using ttsim in daily development workflows. It covers configuration requirements, running workloads, understanding numerical accuracy expectations, and troubleshooting common issues.

**Scope:** This page focuses on the practical aspects of using the simulator once it has been installed. For initial setup instructions, see [Getting Started](https://deepwiki.com/tenstorrent/ttsim/2-getting-started). For detailed architectural information about how ttsim works internally, see [Architecture](https://deepwiki.com/tenstorrent/ttsim/3-architecture). For detailed reference information about specific topics, see the child pages: [Configuration](https://deepwiki.com/tenstorrent/ttsim/4.1-configuration), [Running Workloads](https://deepwiki.com/tenstorrent/ttsim/4.2-running-workloads), [Numerical Accuracy](https://deepwiki.com/tenstorrent/ttsim/4.3-numerical-accuracy), and [Error Reference](https://deepwiki.com/tenstorrent/ttsim/4.4-error-reference).

**Sources:**[README.md 1-121](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L121)

* * *

## Development Workflow with ttsim

The typical development workflow when using ttsim involves an iterative cycle of writing code, configuring the environment, running simulations, and debugging issues. The simulator serves as a substitute for physical Tenstorrent hardware during the development phase.

**Workflow Steps:**

1.   **Code Development:** Write applications using the TT-Metalium software stack
2.   **Environment Configuration:** Set required environment variables to enable simulator mode
3.   **SOC Descriptor Verification:** Ensure the appropriate `soc_descriptor.yaml` file is present
4.   **Execution:** Run workloads through TT-Metalium, which routes them to ttsim
5.   **Result Analysis:** Evaluate outcomes (bit-exact, approximate, or error)
6.   **Iteration:** Debug and refine based on simulator feedback
7.   **Silicon Validation:** Final testing on physical hardware

**Sources:**[README.md 21-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L21-L61)[README.md 78-103](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L78-L103)

* * *

## Configuration Quick Reference

The simulator requires specific environment variables and file placement to function correctly. All configuration must be in place before TT-Metalium is invoked.

### Configuration Checklist

| Requirement | Variable/File | Valid Values | Mandatory |
| --- | --- | --- | --- |
| TT-Metal Path | `TT_METAL_HOME` | Absolute path to tt-metal installation | Yes |
| Simulator Selection | `TT_METAL_SIMULATOR` | Path to `libttsim_wh.so` or `libttsim_bh.so` | Yes |
| Dispatch Mode | `TT_METAL_SLOW_DISPATCH_MODE` | `1` (fast dispatch not supported) | Yes |
| SOC Descriptor | `soc_descriptor.yaml` | Must be in same directory as `.so` file | Yes |

**Example Configuration for Wormhole:**

**SOC Descriptor Placement:**

The `soc_descriptor.yaml` file must be co-located with the simulator binary. For Wormhole, copy `wormhole_b0_80_arch.yaml`; for Blackhole, copy `blackhole_140_arch.yaml` from the TT-Metalium installation.

**Sources:**[README.md 23-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L23-L61)[README.md 63-64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L63-L64)

* * *

## Execution Patterns

### Running TT-Metalium Examples

TT-Metalium examples and tests can run directly on ttsim with the appropriate environment configuration:

### Running TTNN Tests

TTNN (Tenstorrent Neural Network) workloads also execute through the same mechanism:

### Command-Line Pattern

The general execution pattern follows this structure:

```
TT_METAL_SLOW_DISPATCH_MODE=1 <tt-metalium-executable-or-test>
```

The `TT_METAL_SLOW_DISPATCH_MODE=1` prefix is mandatory for all simulator executions due to current limitations in fast dispatch support.

**Sources:**[README.md 50-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L50-L61)[README.md 63-64](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L63-L64)

* * *

## Understanding Simulator Behavior

### Numerical Fidelity Model

The simulator aims for **bit-exact** numerical results relative to silicon. This means matching all hardware computations bit-for-bit, including the precise bit representation of NaNs and special floating-point values.

### Current Implementation Status

| Component | Status | Notes |
| --- | --- | --- |
| Unpacker | Fully Bit-Accurate | Believed to match silicon exactly |
| SFPU | Fully Bit-Accurate | Believed to match silicon exactly |
| Packer | Fully Bit-Accurate | Believed to match silicon exactly |
| FPU MOV* | Fully Bit-Accurate | Move operations match exactly |
| FPU GMPOOL | Fully Bit-Accurate | Global max pooling matches exactly |
| FPU ELW* | Not Yet Bit-Accurate | Element-wise operations being refined |
| FPU MVMUL | Not Yet Bit-Accurate | Matrix-vector multiply being refined |
| FPU GAPOOL | Not Yet Bit-Accurate | Global average pooling being refined |

**Sources:**[README.md 78-103](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L78-L103)

### Sources of Divergence

Even with bit-exact implementation goals, certain scenarios can produce different results between simulator and silicon:

| Scenario | Reason | Mitigation |
| --- | --- | --- |
| Timing-dependent operation ordering | Simulator may evaluate operations in different order than silicon | Serialize operations explicitly or avoid order-dependent algorithms |
| Hardware entropy sources | Simulator does not replicate random number generation | Use deterministic seeding or avoid RNG reads |
| Performance counters | Timing information differs between simulator and silicon | Do not rely on cycle counts or timers for functional logic |
| Missing synchronization | Memory ordering may differ without proper fences | Add explicit synchronization primitives |
| UndefinedBehavior execution | ISA violations produce unpredictable results | Follow ISA specification requirements |

**Sources:**[README.md 85-97](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L85-L97)

* * *

## Error Handling and Diagnostics

The simulator categorizes error messages to help diagnose issues quickly. Understanding these categories enables faster troubleshooting.

### Error Category Reference

| Category | Meaning | Action |
| --- | --- | --- |
| `UndefinedBehavior` | ISA specification violation | Review code against ISA requirements |
| `UnpredictableValueUsed` | Using value with unpredictable state | Check initialization and data flow |
| `NonContractualBehavior` | Relying on unspecified behavior | Use only documented guarantees |
| `UntestedFunctionality` | Feature implemented but not fully tested | Report results; feature may become available |
| `UnimplementedFunctionality` | Feature not yet implemented | File issue; planned for future support |
| `UnsupportedFunctionality` | Feature unlikely to be implemented | Provide justification if needed |
| `MissingSpecification` | Internal specification work required | Internal team will address |
| `SystemError` | OS-level error | Check file permissions, disk space, etc. |
| `ConfigurationError` | Environment or configuration issue | Verify environment variables and files |
| `AssertionFailure` | Internal simulator bug | File bug report with reproduction steps |

**Sources:**[README.md 66-76](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L66-L76)

* * *

## Common Usage Patterns

### Pattern 1: Rapid Prototyping

Use ttsim for quick iteration during algorithm development:

### Pattern 2: CI/CD Integration

Integrate ttsim into continuous integration pipelines:

### Pattern 3: Multi-Architecture Testing

Test code across both Wormhole and Blackhole architectures:

**Sources:**[README.md 40-61](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L40-L61)

* * *

## Performance Characteristics

### Speed Expectations

The simulator is slower than physical silicon but fast enough for productive development. Typical performance characteristics:

*   **Relative Speed:** Significantly slower than silicon
*   **Use Case:** Development and testing, not production workloads
*   **Trade-off:** Slower execution in exchange for accessibility without hardware

### When to Use Simulator vs. Silicon

| Scenario | Recommendation |
| --- | --- |
| Algorithm prototyping | Use simulator |
| Initial debugging | Use simulator |
| Frequent iteration | Use simulator |
| CI/CD testing | Use simulator |
| Performance tuning | Use silicon |
| Production deployment | Use silicon |
| Final validation | Use silicon |

**Sources:**[README.md 3-6](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L3-L6)

* * *

## Integration with TT-Metalium

The simulator integrates seamlessly with TT-Metalium through a simple API. From the developer's perspective, the interface is transparent:

### API Transparency

The TT-Metalium software stack abstracts the underlying execution environment. User code does not need modification to switch between simulator and silicon—only environment variables change:

| Execution Target | Configuration |
| --- | --- |
| Simulator | Set `TT_METAL_SIMULATOR` to `.so` path |
| Silicon | Leave `TT_METAL_SIMULATOR` unset |

**Sources:**[README.md 8-10](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L8-L10)[README.md 41-42](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L41-L42)

* * *

## Best Practices

### Configuration Management

1.   **Use Shell Scripts:** Create shell scripts to set up environments consistently
2.   **Document Requirements:** Keep configuration requirements in project README files
3.   **Verify Before Execution:** Check that all required environment variables are set
4.   **Version Pin:** Use specific simulator versions in production CI/CD pipelines

### Development Workflow

1.   **Start with Simulator:** Develop and debug on simulator first
2.   **Test Incrementally:** Test small changes frequently rather than large batches
3.   **Understand Limitations:** Be aware of bit-exact vs. approximate operations
4.   **Validate on Silicon:** Always perform final validation on physical hardware
5.   **Report Issues:** File GitHub issues for bugs or missing features

### Error Handling

1.   **Read Error Categories:** Error category indicates appropriate action
2.   **Check Configuration First:** Most errors stem from environment setup
3.   **Review ISA Documentation:** For UndefinedBehavior errors, consult ISA specs
4.   **Provide Reproduction Steps:** When filing issues, include minimal reproduction

**Sources:**[README.md 104-120](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L104-L120)

* * *

## Troubleshooting Quick Reference

### Common Issues and Solutions

| Symptom | Likely Cause | Solution |
| --- | --- | --- |
| "Cannot find simulator" | `TT_METAL_SIMULATOR` not set | Set environment variable to `.so` path |
| "SOC descriptor not found" | `soc_descriptor.yaml` missing | Copy appropriate YAML to simulator directory |
| Fast dispatch error | Using fast dispatch mode | Set `TT_METAL_SLOW_DISPATCH_MODE=1` |
| "TT_METAL_HOME not set" | Missing environment variable | Export path to tt-metal installation |
| Numerical mismatch | FPU operation not bit-exact | Check component status table |
| Timing-related divergence | Non-deterministic operation order | Serialize operations explicitly |

### Diagnostic Commands

**Sources:**[README.md 110-115](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L110-L115)

* * *

## Next Steps

For detailed information on specific topics:

*   **Configuration details:** See [Configuration](https://deepwiki.com/tenstorrent/ttsim/4.1-configuration) for comprehensive environment variable reference and troubleshooting
*   **Running workloads:** See [Running Workloads](https://deepwiki.com/tenstorrent/ttsim/4.2-running-workloads) for command-line usage and examples
*   **Numerical accuracy:** See [Numerical Accuracy](https://deepwiki.com/tenstorrent/ttsim/4.3-numerical-accuracy) for bit-exactness guarantees and determinism
*   **Error handling:** See [Error Reference](https://deepwiki.com/tenstorrent/ttsim/4.4-error-reference) for complete error category descriptions and remediation

For information about contributing issues or feature requests, see [Contributing](https://deepwiki.com/tenstorrent/ttsim/5-contributing).

For architectural details about how ttsim works internally, see [Architecture](https://deepwiki.com/tenstorrent/ttsim/3-architecture).

**Sources:**[README.md 1-121](https://github.com/tenstorrent/ttsim/blob/67b1733e/README.md?plain=1#L1-L121)

Dismiss
Refresh this wiki

Enter email to refresh