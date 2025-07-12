# PCIe LTSSM – Clarity Companion

This project provides a modern, readable reference for the PCI Express LTSSM from `Detect` to `L0`.  
The goal is to retain 100% spec accuracy while improving clarity using structured technical writing.

## Why?

The PCIe Base Spec is comprehensive—but difficult to read and implement directly.  
This project helps engineers, validation teams, and educators by restructuring the logic into clearly defined states.

## Format

Each LTSSM state includes:
- Purpose
- Entry conditions
- Required behavior
- Exit conditions
- Register/variable resets
- Spec references

## States Available

- [Detect.Quiet](./states/Detect.Quiet.md)
- [Detect.Active](./states/Detect.Active.md)

## Status

This is an ongoing experiment. Feedback and contributions are welcome.

## License

This content is available under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
