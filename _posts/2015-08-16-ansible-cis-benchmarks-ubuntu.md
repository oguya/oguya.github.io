---
layout: post
title:  "Deploying CIS Benchmarks on Ubuntu 14.04 hosts using Ansible"
date:   2015-08-16 16:06
excerpt: "Center for Internet Security(CIS) provides well-defined, unbiased & consensus-based industry best practices to help organizations assess and improve their security. In this blog post, we'll talk about these benchmarks & how you can deploy most, if not all, on your Ubuntu 14.04 box."
category: Linux
tags: ansible,cis,audit
---

## CIS Security Benchmarks

The CIS(Center for Internet Security)[<sup>[1]</sup>](https://benchmarks.cisecurity.org/about/) Security Benchmarks program provides well-defined, unbiased & consensus-based industry best practices to help organizations assess and improve their security. Resources include secure configuration benchmarks, automated configuration assessment tools and content, security metrics and security software product certifications.
CIS also provides auditing tools such as CIS-CAT[<sup>[2]</sup>](https://benchmarks.cisecurity.org/downloads/audit-tools/index.cfm#cis-cat) for analyzing & monitoring security benchmarks.

CIS Security benchmarks & recommendations are grouped into two(2) level - Level 1 & Level 2.

**Level 1**

Items in this profile intend to:

- be practical and prudent
- provide a clear security benefit
- have minimum effect on production workload

**Level 2**

This is an extension of Level 1 but with the following characteristics:

- intended for environments or use cases where security is paramount
- acts as a defense in depth measure
- have great effect on production workload

To indicated compliance with a given recommendation, a benchmark score of either _Scored_ or _Not Scored_ is awarded:

**Scored**

- Failure to comply with _Scored_ recommendations will decrease the final benchmark score
- Compliance with _Scored_ recommendations will increase the final benchmark score

**Not Scored**

- Failure to comply with _Not Scored_ recommendations will not decrease the final benchmark score
- Compliance with _Not Scored_ recommendations will not increase the final benchmark score


## Putting it all together with Ansible

After carefully reading the [CIS Ubuntu 14.04 LTS Server Benchmark documentation](https://benchmarks.cisecurity.org/downloads/show-single/?file=ubuntu1404.100), I created an [Ansible playbook](https://github.com/oguya/cis-ubuntu-14-ansible#ansible-playbook) that would allow users to audit & configure their systems to meet at most all of the CIS Security benchmarks.

Please, please, please have at the [CIS Ubuntu 14.04 LTS Server Benchmark documentation](https://benchmarks.cisecurity.org/downloads/show-single/?file=ubuntu1404.100) before running this playbook & always do a dry run first using [Ansible's Check Mode](http://docs.ansible.com/playbooks_checkmode.html) so as to see what to expect.

## Contribution
As always, [Pull requests](https://github.com/oguya/cis-ubuntu-14-ansible/pulls) and [Github issues](https://github.com/oguya/cis-ubuntu-14-ansible/issues) are all welcome!

### Links
1. [About CIS Security Benchmarks](https://benchmarks.cisecurity.org/about/)
2. [CIS-CAT Benchmark Assessment Tool](https://benchmarks.cisecurity.org/downloads/audit-tools/index.cfm#cis-cat)
3. [CIS Ubuntu 14.04 Benchmarks Ansible Playbook](https://github.com/oguya/cis-ubuntu-14-ansible)
