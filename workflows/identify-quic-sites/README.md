# 1. Identify QUIC Sites

This workflow downloads and sanitises the TLD list and popular domain lists, queries domains for their support for QUIC, and selects URLs that support common QUIC versions.  

该工作流程下载和清理顶级域名列表和热门域名列表，查询域名是否支持QUIC，并选择支持常见QUIC版本的URL。


## Data Availability Statements

The URL lists and list of top-level domains used to support the findings of this workflow are available online.
We provide them, in addition to the results of the QUIC support scan, as they require interaction with external websites.

用于支持此工作流程的URL列表和顶级域名列表的结果可在网上获得。我们提供这些结果以及QUIC支持扫描的结果，因为它们需要与外部网站进行交互。

### Dataset List

| Data file                                                 | Source        | Notes                 | Provided |
|-----------------------------------------------------------|---------------|-----------------------|----------|
| `results/all-domains/alexa-1m-2020-06-22.csv`             | [Alexa 1M]    | Popular domains       | yes      |
| `results/all-domains/majestic-1m-2020-06-22.csv`          | [Majestic 1M] | Popular domains       | yes      |
| `results/all-domains/umbrella-1m-2020-06-22.csv`          | [Umbrella 1M] | Popular domains       | yes      |
| `results/all-domains/tlds-alpha-by-domain-2020-06-22.txt` | [IANA]        | Top-level domain list | yes      |
| `results/profile-domains/profile-results.csv.gz`          | This workflow | Raw QUIC scan results | yes      |


## Computational Requirements

### Description of Programs

- `scripts/filter-domain-list`: Filters invalid, similar, or duplicate entries from the domain list. 从域名列表中过滤无效、相似或重复的条目
- `scripts/profile-domains`: Queries web-servers for their QUIC support. 查询网站服务器是否支持QUIC
- `notebooks/profile-domain-inspection.ipynb`: Calculates summary statistics from the scan results. 从扫描结果计算摘要统计信息
- `Snakefile`, `rules/`: Orchestrates running the above scripts and notebook. 负责运行上述脚本和note

### Memory and Runtime Requirements

Scanning occurs asynchronously on a single core and takes ~17 hours.

扫描在单个核心上异步进行，需要大约17小时


## Instructions

Follow the instructions in the main [README](../../README.md), then run the workflow with:

```bash
# Perform a dry-run
snakemake -n -j
# Make all targets in the workflow
snakemake -j
```

Since the raw QUIC scan results are already provided, this will create the final URLs from the scan results.
Use `snakemake -F -j` to force running all of the rules, or see `snakemake --help` for other run options.

于已提供原始的QUIC扫描结果，这将从扫描结果创建最终的URL列表。
使用 snakemake -F -j 强制运行所有规则，或者查看 snakemake --help 获取其他运行选项。

<!-- Links -->
[Alexa 1M]: http://s3.amazonaws.com/alexa-static/top-1m.csv.zip
[Majestic 1M]: http://downloads.majestic.com/majestic_million.csv
[Umbrella 1M]: http://s3-us-west-1.amazonaws.com/umbrella-static/top-1m.csv.zip
[IANA]: https://data.iana.org/TLD/tlds-alpha-by-domain.txt
