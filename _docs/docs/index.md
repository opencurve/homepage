---
title: Welcome
permalink: /docs/home/
redirect_from: /docs/index.md
---

![curve logo](/assets/img/curve-logo1.png)

## Curve

Curve is a distributed storage system designed and developed by NetEase, featured with high performance, easy operation and cloud native. Curve is compose of CurveBS(Curve Block Storage) and CurveFS(Curve FileSystem). CurveBS supports snapshot, clone, and recover, also supports virtual machines with qemu and physical machine with nbd. CurveFS supports POSIX based on Fuse.

The Roadmap of Curve: [Roadmap](https://github.com/opencurve/curve/wiki/Roadmap)
## Curve vs Ceph

Curve: v1.2.0

Ceph: L/N
### Performance
Curve random read and write performance far exceeds Ceph in the block storage scenario.

Environment：3 replicas on a 6-node cluster, each node has 20xSATA SSD, 2xE5-2660 v4 and 256GB memory.

Single Vol：
![1-nbd](/assets/img/1-nbd-en.png)

Multi Vols：
![10-nbd](/assets/img/10-nbd-en.png)


### Stability

The stability of the common abnormal Curve is better than that of Ceph in the block storage scenario.

| Fault Case | One Disk Failure | Slow Disk Detect | One Server Failure | Server Suspend Animation |
| ---- | ---- | ---- | ---- | ---- |
| Ceph | jitter 7s | Continuous io jitter | jitter 7s | unrecoverable |
| Curve | jitter 4s | no effect | jitter 4s | jitter 4s |

### Ops

Curve ops is more friendly than Curve in the block storage scenario.

| Ops scenarios | Upgrade clients | Balance |
| ---- | ---- | ---- |
| Ceph | do not support live upgrade | via plug-in with IO influence |
| Curve | support live upgrade with second jitter | auto with no influence on IO |

## Design Documentation

- Wanna have a glance at CURVE? Click here for [Intro to CURVE](https://www.opencurve.io/)!
- Want more details? Our documentation for every component:
  - [NEBD](../nebd_en.md)
  - [MDS](../mds_en.md)
  - [Chunkserver](../chunkserver_design_en.md)
  - [Snapshotcloneserver](../snapshotcloneserver_en.md)
  - [CURVE quality control](../quality_en.md)
  - [CURVE monitoring](../monitor_en.md)
  - [Client](../client_en.md)
  - [Client Python API](../curve-client-python-api_en.md)
- Application based on CURVE
  - [Work with k8s](../k8s_csi_interface_en.md)

## Quick Start

Want to try on it? Take it easy! We'll help you step by step, but make sure you've read this [Special Statement](../deploy_en.md#special-statement) before you start.

### Deploy an all-in-one environment (to try how CURVE works)

[Deploy on single machine](../deploy_en.md#deploy-on-single-machine)

### Deploy multi-machine cluster (try it in production environment)

[Deploy on multiple machines](../deploy_en.md#deploy-on-multiple-machines)

### curve_ops_tool introduction

[curve_ops_tool introduction](../curve_ops_tool_en.md)

## For Developers

### Deploy build and development environment

[development environment deployment](../build_and_run_en.md)

### Compile test cases and run
[test cases compiling and running](../build_and_run_en.md#test-case-compilation-and-execution)

### Coding style guides
CURVE is coded following [Google C++ Style Guide strictly](https://google.github.io/styleguide/cppguide.html). Please follow this guideline if you're trying to contribute your codes.

### Code coverage requirement
1. Unit tests: Incremental line coverage ≥ 80%, incremental branch coverage ≥ 70%
2. Integration tests: Measure together with unit tests, and should fulfill the same requirement
3. Exception tests: Not required yet

### Other processes

After finishing the development of your code, you should submit a pull request to master branch of CURVE and fill out a pull request template. The pull request will trigger the CI automatically, and the code will only be merged after passing the CI and being reviewed.

For more detail, please refer to [CONTRIBUTING](https://github.com/opencurve/curve/blob/master/CONTRIBUTING.md).

## Release Cycle
- CURVE release cycle：Half a year for major version, 1~2 months for minor version

- Versioning format: We use a sequence of three digits and a suffix (x.y.z{-suffix}), x is the major version, y is the minor version, and z is for bugfix. The suffix is for distinguishing beta (-beta), RC (-rc) and GA version (without any suffix). Major version x will increase 1 every half year, and y will increase every 1~2 months. After a version is released, number z will increase if there's any bugfix.

## Branch

All the developments will be done under master branch. If there's any new version to establish, a new branch release-x.y will be pulled from the master, and the new version will be released from this branch.

## Feedback & Contact

- [Github Issues](https://github.com/openCURVE/CURVE/issues)：You are sincerely welcomed to issue any bugs you came across or any suggestions through Github issues. If you have any question you can refer to our FAQ or join our user group for more details.
- [FAQ](https://github.com/openCURVE/CURVE/wiki/CURVE-FAQ)：Frequently asked question in our user group, and we'll keep working on it.
- User group：We use Wechat group currently.

![curve wechat](/assets/img/curve-wechat.jpeg)
