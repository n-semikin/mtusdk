# MAGTEK MTU SDK

## Repository Overview

This repository provides versioned sets of `.so` and `.jar` files for easy distribution to client production and
development systems. You can update POS systems and manage different file versions seamlessly when MagTek releases 
new updates. The `mtusdk-native` module includes the `SoExtractor` class, which extracts `.so` files to the root 
directory (what is `root directory`??? the `root` of what?). Files will only be replaced if their sizes differ from 
the existing files. 
The client system must add the extraction path to the classpath (unclear, need to be expanded).

## Project Structure

- **`mtusdk/com/magtek/mtusdk-java`**: This folder contains the Java `.jar` file provided by MagTek as part of the [MagTek Universal SDK for MMS Devices (Java) - PN 1000008300 v100](https://www.magtek.com/Content/SoftwarePackages/1000008300.zip).

- **`mtusdk/com/magtek/mtusdk-native`**: This contains the compiled `.so` files based on the [MagTek Universal SDK for MMS Devices (Linux) - PN 1000009347](https://www.magtek.com/Content/SoftwarePackages/1000009347.zip). This Maven library also includes a self-extraction API, and it can be run from the command line for testing.

- **`mtaesdukpt-native`**: This contains the compiled `.so` files based on MagTek DKUPT AES project to decrypt card holder name and expiration date.

## Publishing Maven dependency

Artifacts are published as Maven dependencies. 
How it works:
- there are multiple artifacts folders: `mtusdk-java`, `mtusdk-native`, `mtaesdukpt-java` and `mtaesdukpt-native`
- each artifact folder contains version folders (like `2024-09-05` or `1000009782-101` or `preliminary-2024-09-05-5`)
- each version folder contains:
  - a jar-artifact with name like `<artifactId>-<version>(-<classifier>).jar`. This is a built artifact on some dev
station and pushed to the repository.
  - a pom-file with name `<artifactId>-<version>(-<classifier>).pom`. This is a maven pom-file that describes the 
artifact and its dependencies.
  - `src` folder with additional classes and resources that used to build the jar-artifact.
- when a new version of artifact is added to the repository and pushed to the GitHub it becomes available for
  downloading and for usage in the Maven projects as a dependency. To use it as a Maven dependency you need to add the
  GitHub-repository to your `pom.xml` file as a Maven repository (see example below). 

How does Maven find dependencies when they are published to a GitHub repository:
- Maven uses default URL template to find the artifact in the repository: `{repo-url}/<groupId path>/<artifactId>/<version>/<artifactId>-<version>(-classifier).{pom|jar}`
- Maven uses the `pom` file to find the artifact and its dependencies. Without `pom` file Maven considers the 
repository as broken.

So Maven uses URLs to get info about the artifact. With the proper folder structure GitHub repository can follow the
Maven URL template and provide the artifact to the Maven client.

## Usage Instructions

```xml
<repositories>
    <repository>
        <id>central</id>
        <url>https://repo1.maven.org/maven2</url>
    </repository>
    <repository>
        <id>github-mtusdk-repo</id>
        <url>https://github.com/SmartTAB/mtusdk/raw/main/</url>
    </repository>
</repositories>


<!-- Java dependency -->
<dependency>
	<groupId>com.magtek</groupId>
	<artifactId>mtusdk-java</artifactId>
	<version>preliminary-2024-09-05</version>
</dependency>

<!-- Native Linux .so files -->
<dependency>
	<groupId>com.magtek</groupId>
	<artifactId>mtusdk-native</artifactId>
	<version>preliminary-2024-09-05</version>
	<classifier>linux-x86_64</classifier>
</dependency>

<dependency>
	<groupId>com.magtek</groupId>
	<artifactId>mtaesdukpt-native</artifactId>
	<version>1000009782-101</version>
	<classifier>linux-x86_64</classifier>
</dependency>
```