# \[DEPRECATED\] Manual: Gravity Extractor one-click deployment

## Abstract

This page provides a one-click deployment manual of Gravity price extractor using the Linode hosting service.

Linode provides StackScripts: scripts that enable initial machine configuration on the first boot-up.

## Requirements

In order to deploy the Gravity extractor image, you must be registered on Linode.

## Guide

1. First, open the StackScript provided below:

[https://cloud.linode.com/stackscripts/664034](https://cloud.linode.com/stackscripts/664034)

[![alt text](https://camo.githubusercontent.com/fca152f9126e48fd9f161cd79e6b6483e213ef0a/68747470733a2f2f692e696d6775722e636f6d2f6734457333684a2e706e67)](https://camo.githubusercontent.com/fca152f9126e48fd9f161cd79e6b6483e213ef0a/68747470733a2f2f692e696d6775722e636f6d2f6734457333684a2e706e67)

1. Click 'Deploy New Linode'

[![alt text](https://camo.githubusercontent.com/db46954b73b0a932392bdcfc628b90a86505838d/68747470733a2f2f692e696d6775722e636f6d2f565632696950562e706e67)](https://camo.githubusercontent.com/db46954b73b0a932392bdcfc628b90a86505838d/68747470733a2f2f692e696d6775722e636f6d2f565632696950562e706e67)

As shown, a StackScript for the Gravity extractor Linode image is already provided to enable machine configuration on the first boot-up.

3. Click on "Details" to see further information. Make sure that the name of the StackScript corresponds with the one on the screenshot.

[![alt text](https://camo.githubusercontent.com/c3f954d5c552e588a5497480bb07bb627e2bb434/68747470733a2f2f692e696d6775722e636f6d2f796b69436c69332e706e67)](https://camo.githubusercontent.com/c3f954d5c552e588a5497480bb07bb627e2bb434/68747470733a2f2f692e696d6775722e636f6d2f796b69436c69332e706e67)

4. Choose a suitable instance image. Please note that the StackScript only works on Ubuntu.

[![alt text](https://camo.githubusercontent.com/a2d67e6d5d3bd2fbbb8513e3633b7ded620dcb17/68747470733a2f2f692e696d6775722e636f6d2f775646656f766c2e706e67)](https://camo.githubusercontent.com/a2d67e6d5d3bd2fbbb8513e3633b7ded620dcb17/68747470733a2f2f692e696d6775722e636f6d2f775646656f766c2e706e67)

5. Next, specify the server region and a root password.

The server boot-up process usually lasts around five to seven minutes.

[![alt text](https://camo.githubusercontent.com/7a55c409a1424fb0eff5c6bdc21115b30004d2d7/68747470733a2f2f692e696d6775722e636f6d2f536246735447452e706e67)](https://camo.githubusercontent.com/7a55c409a1424fb0eff5c6bdc21115b30004d2d7/68747470733a2f2f692e696d6775722e636f6d2f536246735447452e706e67)

6. Launch the server on the 8090 port. In this case: `http://45.79.204.177:8090`

`/info` - Resolves info. Here, the extractor details are presented.

[![alt text](https://camo.githubusercontent.com/dffb678e59f9bb0f0f5cfc63eb62f3bb5960596f/68747470733a2f2f692e696d6775722e636f6d2f3167346a42754f2e706e67)](https://camo.githubusercontent.com/dffb678e59f9bb0f0f5cfc63eb62f3bb5960596f/68747470733a2f2f692e696d6775722e636f6d2f3167346a42754f2e706e67)

`/extracted` - Extracted value

[![alt text](https://camo.githubusercontent.com/712f00c654c7e19e7d86a581122f8931061615b1/68747470733a2f2f692e696d6775722e636f6d2f7037633759706a2e706e67)](https://camo.githubusercontent.com/712f00c654c7e19e7d86a581122f8931061615b1/68747470733a2f2f692e696d6775722e636f6d2f7037633759706a2e706e67)

