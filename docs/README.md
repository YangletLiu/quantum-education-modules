# Quantum Education Modules Documentation

This folder contains the setup for hosting the **Read the Docs** [website](https://quantum-education-modules.readthedocs.io/en/latest/) for this GitHub.

## Structure

The documentation is organized as follows:

- **Home**: Homepage with links to documentation and modules
- **[User Documentation](source/user/)**: Overview of project, roadmap, usage, and how to contribute.
- **[Introductory Modules](source/introductory)**: Section for all the introductory modules in [../Modules/Concepts](../Modules/Concepts).
- **[Intermediate Modules](source/intermediate)**: Section for all intermediate modules in [../Modules/Algorithms](../Modules/Algorithms).
- **[Advanced Modules](source/advanced)**: Section for all advanced modules in [../Modules/Applications](../Modules/Applications).
- **[Quantum Circuit Design](source/quantum_circuit_design)**: Details on an accepted project for a [workshop](https://april-tools.github.io/colorai/accepted.html), Reinforcement Learning for Quantum Circuit Design

Each folder contains either .rst or .nblink files that can be modified to change the content on the site.

Alternatively, you can clone this repository and build the documentation locally, using conda:

```bash
# Clone the repository
git clone https://github.com/YangletLiu/quantum-education-modules.git

# Navigate to the docs folder
cd quantum-education-modules/docs

# Install dependencies
conda env create --name test --file environment.yml

# Activate environment
conda activate test

# Build the documentation
sphinx-build -b html source/ _build/
```

The output HTML files will be located in the `_build/` directory.

## Contributing

We welcome contributions from the community to enhance and expand these educational resources. Please read our [Contributing Guide](https://quantum-education-modules.readthedocs.io/en/latest/user/contribute.html) for more details on how to get involved.
