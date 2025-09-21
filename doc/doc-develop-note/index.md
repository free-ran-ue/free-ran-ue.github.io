# Develop Note

> [!Important]
> Beleive myself that I can do everything.

## 20250921 Config Validator

While using free-ran-ue, validator will be the first guard to check the configurations' information is correct.

For gNB's validator, it is contributed by [Dan](https://github.com/c9274326). Welcome for new  contributor!!

## 20250919 Xn Interface in Config

I've integrated all the Xn related information in to `xnInterface` in gNB configs.

## 20250910 Config Description

For configurations about gNB and UE, there are clear description for new users.

## 20250908 ULCL

free-ran-ue has supported ULCL deployment!

## 20250904 Logo and Favicon

The logo and favicon are designed by [Lily](https://github.com/lilywuuuuu). Welcoms for new contributor!!

## 20250903 YARN Dependabot & Frontend Files

The dependabot will help the project to update frontend's deps.

And, I make the frontend file path will be read from config instead of hardcode in backend.

## 20250902 GTP5G

I updated the description of gtp5g in quickstart pages.

## 20250828 Console UE & Docker Build Test

Now, console will desplay UE's info!

The ci actions will also support for test image build.

## 20250826 Docker

Today I finish the docker deployment in three types:

- Basic Usage
- Static NR-DC
- Dynamic NR-DC

## 20250819 Dynamic NR-DC

After a long long time, I've finished the manual dynamic NR-DC control in free-ran-ue. Now, user can use the console to switch DC status.

## 20250815 GTP Parser

I fixed the bug on parsing GTP header. It will be more formal on handle different header flags.

## 20250807 QoS Flow

I fixed the bug about configuring QoS flow from YAML file. It should be extract via NAS message.

## 20250806 IMSI

Today, free5GC correct the invalid imsi value. I also fix it in UE's NAS build test case.

## 20250731 Specified Flow

I rename the QoS flow as specified flow due to the dual connection does not need to be **QoS**.

Also with implement the iperf namespace for testing throughput.

## 20250724 Static NR-DC

I refactor the RAN UE management in gNB and also fix the magic number issue at `AMFUENGAPID` and `RANUENGAPID`.

It is the day that I finish the **Static NR-DC** feature in free-ran-ue. What a powerful day.

## 20250720 UE De-registration Procedure

I complete the whole process with UE de-registration process. It's time to give a great user guide.

## 20250719 Upgrade go 1.24

Today I bump the go version from 1.22 to 1.24(1.24.5) and also add the linter ci action in github workflows.

At github actions, I enable the dependabot for checking go mod deps version.

## 20250716 Userspace GTP-U: DOWNLINK & Fix SQN Error

It is a great day for finishing the full data plane traffic of user space GTP-U. Now, free-ran-ue can establishment a complete control plane message and funtional data plane trasmission.

It also help me to understand a lot of linux based network setting about routing and namespace ip setting.

I also fix the MAC authentication error caused by sequence number increasement on core network side.

## 20250715 Userspace GTP-U: UPLINK

Today I finished the UPLINK data plane from UE to data network. It is a hard working day for debuging conflict in routing rules with 10.60.0.x source ip.

It also prove that it is available that we do the whole GTP-U packet encapsulating and forwardign in userspace without using kernel module in traditional.

## 20250714 PDU Session Establishment

I finished the PDU session establishment procedure, now it can be observed a correct tcpdump record in pcap files. But it still need to do more handler about processing the NAS message sent from RAN to UE. In current implement, the NAS parts are ignored.

## 20250711 gh-page Deployment and Main Image

Today I have ChatGPT to generate an image for this project and use mkdocs to deploy a github page for presenting free-ran-ue.

## 20250710 UE Registration and Update Logger

I've done the UE registration NAS procedure and the corresponding NGAP action.

For the logger part, I updated the package: [logger-go](https://github.com/Alonza0314/logger-go) to v2.0.3. It can support multi-tags with seperate `LoggerInterface`. As a result, I need to update the log part in free-ran-ue. Now the logger used in this project is more convenient.

## 20250627 Remove Comment Line

Nothin important, just remove a comment line.

## 20250624 Cobra and Test and License

Make the start method encapsolate by cobra-cli. Also construct the unit test in build protocol-message.

The most important thing is set Apache-2.0 license for this project.

## 20250623 NGAP Setup

I've done the basic NGAP setup between gNB and core network.

What a nice start!

## 20250622 Basic Repo Setup

This is the first day of this repo. It would be a crazy idea to construct a new RAN and UE simulator.
But going to do is better than being afraid.

Just do it!
