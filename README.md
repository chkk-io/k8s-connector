# Chkk Kubernetes Connector

## Overview

Chkk Kubernetes Connector is a Kubernetes Operator to deploy and configure the Chkk Cluster Agent in your cluster. With this Operator, you can use a single Custom Resource Definition (CRD) to deploy the Chkk Cluster Agent and its configurations. The Operator reports deployment status, health, and errors in the Operator’s CRD status.

Once you have deployed the Chkk Agent, the Chkk Kubernetes Connector provides the following benefits:

- Validation for your Chkk Agent configurations
- Keeping the Agent up to date with your configuration
- Orchestration for creating and updating Agent resources and filters
- Reporting of Agent configuration status in the Operator’s CRD status

## System Requirements
- **Kubernetes**: Version 1.19 or higher
- **OS/Architecture**: linux/amd64, linux/arm64
- **kubectl**: Version 1.19 or higher
- **Helm**: Version 2 or higher

## Installation
To install the Chkk Kubernetes Connector, log in to your account on chkk.io and follow the detailed instructions provided.

## Documentation
For more information on the Chkk Operator, please refer to the [Chkk Operator Documentation](helm/chkk-operator/README.md).

## Support
For support, troubleshooting, and additional resources, please visit [Chkk Support](https://www.chkk.io/) or open an issue in this repository.


## License
This project is licensed under the Apache License, Version 2.0. See the [LICENSE](LICENSE) file for more details.
