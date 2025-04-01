---
icon: material/handshake
---

# How to Contribute

OSL is open-source at [https://github.com/mshandilya/osl](https://github.com/mshandilya/osl). To contribute:

1. **Fork the Repository**:
   ```bash
   git clone https://github.com/your-username/osl.git
   ```
2. **Create a Branch**:
   ```bash
   git checkout -b feature/your-feature
   ```
3. **Commit Changes**:
   Follow the [Conventional Commits](https://www.conventionalcommits.org/) style.
4. **Submit a Pull Request**:
   Describe your changes clearly in the PR.

Check the [Issues](https://github.com/mshandilya/osl/issues) tab for tasks or propose new features!

## How to Customize

- **Extend the VM**: Add new opcodes in `vm.c` or `stack_vm.py` and update the interpreter.
- **Modify Grammar**: Edit `osl_parser.py` or the unambiguous grammar for new syntax.
- **Codegen Tweaks**: Adjust `codegen.py` to generate custom bytecode.
