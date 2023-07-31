---
title: "Getting Started"
linkTitle: "Getting Started"
weight: 1
description: >-
  Start using the Akash Network and deploy a test workload in less than 5 minutes.
---

{{< rawhtml >}}
<div style="margin: 64px auto;">
  <h5 style="margin-left: 16px; font-weight: bold;">Choose your deployment method</h5>
  <div style="display:table;border-collapse:separate;border-spacing:16px; table-layout: fixed; width: 100%;">
    <div class="landing-box">
      <h5>Deploy with Console</h5>
      <p>Akash Network's GUI</p>
      <a class="more" href="/docs/deployments/deploy-using-console">Deploy <i class="fa fa-arrow-right"></i></a>
    </div>
    <div class="landing-box">
      <h5>Deploy with Cloudmos</h5>
      <p>GUI platform built by Cloudmos.io</p>
      <a class="more" href="/docs/deployments/deploy-using-cloudmos">Deploy <i class="fa fa-arrow-right"></i></a>
    </div>
    <div class="landing-box">
      <h5>Deploy with Terraform</h5>
      <p>Popular IaC tool built by Hashicorp</p>
      <a class="more" href="/docs/deployments/deploy-using-terraform">Deploy <i class="fa fa-arrow-right"></i></a>
    </div>
  </div>
  <div style="display:table;border-collapse:separate;border-spacing:16px; table-layout: fixed; width: 100%;">
    <div class="landing-box">
      <h5>Deploy with CLI</h5>
      <p>The Akash Command Line Interface</p>
      <a class="more" href="/docs/deployments/deploy-using-cli">Deploy <i class="fa fa-arrow-right"></i></a>
    </div>
    <div class="landing-box">
      <h5>Deploy with Client Libraries</h5>
      <p>Client Libraries built in Go & Java</p>
      <a class="more" href="/docs/developers">Deploy <i class="fa fa-arrow-right"></i></a>
    </div>
    <div class="landing-box" style="visibility: none; border: none;">
    </div>
  </div>
</div>
{{< /rawhtml >}}

Select a tab below to view instructions for MacOS, Linux, or compiling from source.

{{< tabpane text=true >}}
{{% tab header="MacOS" %}}
#### Using Homebrew
The simplest way to install Akash is using Homebrew using:

```bash
brew tap akash-network/tap
brew install akash-provider-services
```

#### Downloading Akash Binary
If you do not have homebrew, follow the below steps for installing the Akash Binary.

These commands will retrieve the latest, stable version of the Akash software, store the version in a local variable, and install that version.
```
cd ~/Downloads
curl -sfL https://raw.githubusercontent.com/akash-network/provider/main/install.sh | bash
```
Move the binary file into a directory included in your path
```
sudo mv ./bin/provider-services /usr/local/bin
```

{{% /tab %}}

{{% tab header="Linux" %}}
The simplest way to install Akash is using Homebrew using:

```
brew untap ovrclk/tap
brew tap akash-network/tap
brew install akash-provider-services
```

If you do not have homebrew, follow the below steps for installing the Akash Binary.

#### Download Akash Binary

These commands will retrieve the latest, stable version of the Akash software\_**,**\_ store the version in a local variable, and install that version.

```
cd ~

apt install jq -y

apt install unzip -y

curl -sfL https://raw.githubusercontent.com/akash-network/provider/main/install.sh | bash
```

#### Add Akash Install Location to User’s Path

Add the software’s install location to the user’s path for easy use of Akash commands.

**NOTE:** Below we provide the steps to add the Akash install directory to a user’s path on a Linux Ubuntu server. Please take a look at a guide for your operating system and how to add a directory to a user’s path.

Open the user’s path file in an editor:

```
vi /etc/environment
```

View within text editor prior to the update:

```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
```

Add the following directory, which is the Akash install location, to `PATH`. In this example the active user is root. If logged in as another username, replace /root with your current/home directory.

```
/root/bin
```

View within the text editor following the update:

```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/root/bin"
```

### Make the Path Active in the Current Session

```
. /etc/environment
```
{{% /tab %}}
{{% tab header="Source" %}}

Installing Akash suite from source:

```
$ go get -d github.com/akash-network/provider
$ cd $GOPATH/src/github.com/akash-network/provider
$ AKASH_NET="https://raw.githubusercontent.com/ovrclk/net/master/mainnet"
$ AKASH_VERSION="$(curl -s https://api.github.com/repos/akash-network/provider/releases/latest | jq -r '.tag_name')"
$ git checkout "v$AKASH_VERSION"
$ make deps-install
$ make install
```

Akash is developed and tested with [golang 1.16+](https://golang.org/). Building requires a working [golang](https://golang.org/) installation, a properly set `GOPATH`, and `$GOPATH/bin` present in `$PATH`.

Once you have the dependencies properly setup, download and build `akash` using `make install`
{{% /tab %}}
{{< /tabpane >}}

### Verify Akash Installation
Verify the installation by using a simple command to check the Akash version
```
provider-services version
```