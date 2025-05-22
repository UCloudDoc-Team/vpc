## 构建IPV6双栈网络

1. 创建双栈VPC

支持在创建虚拟私有网络VPC时，开启IPv6功能，由系统会自动分配IPv6网段

![img](/images/391e81fa-7738-4f20-98af-d559280999ae.png)

支持存量私有网络VPC开启IPV6网络

![img](https://ucloud-llk.feishu.cn/space/api/box/stream/download/asynccode/?code=NTU0ZTdkNGQyMDNmMmY1Mzg3ZGU1MDRhMzU0N2NjOWZfUTZyb2pRNFE0M0Z5WmExYmN2YmpObEtIYlhKbko2NTRfVG9rZW46SVVvUmJ2aHZYb002Qld4TjltQ2N0ZWNWbm1iXzE3NDc4OTg5MzM6MTc0NzkwMjUzM19WNA)

1. 创建双栈子网

支持在创建子网时，开启IPv6功能，由系统会自动分配IPv6网段

![img](https://ucloud-llk.feishu.cn/space/api/box/stream/download/asynccode/?code=MTljYjdjNTVhMjExYzMxMTNhMmM4N2FhZTRlMDZlZTJfWmY1NjNZdlRISm1QYXl2NUVpOHpxZHVYR1pSWUZBVEtfVG9rZW46UHNjT2JOVEw2b1ZjM2N4TmlOWGNqTkJQbkJoXzE3NDc4OTg5MzM6MTc0NzkwMjUzM19WNA)

支持存量子网开启IPV6网络

![img](https://ucloud-llk.feishu.cn/space/api/box/stream/download/asynccode/?code=YTFiNTkzZjNhMzJkZjFlZTg5MDdhZjhiODE0NGIyM2Nfd1duQlhXQW1vdVBLR1FNRDloUjFoNGlNR3ROZlJBN1dfVG9rZW46SHUza2JGMjNlbzc0Slp4Zk05RGNqU0d4bnZjXzE3NDc4OTg5MzM6MTc0NzkwMjUzM19WNA)

1. 创建双栈资源

以云主机为例，创建双栈云主机时，需要选择开启了IPV6功能的VPC和子网，且选择镜像为centos7.9 EOL 或Debian11.7才支持分配IPV6 IP

![img](https://ucloud-llk.feishu.cn/space/api/box/stream/download/asynccode/?code=YzcwYmRkMTcyOWJmNjcwN2M5YzFkNTFjZWQzN2E2MDNfMnE5bXh6N3RGSHRCa0V2MGJYOXhFY0JPeUtaYUwwVzVfVG9rZW46Q1RlRmJpdnFvb2lnNWR4dlZhQWNwSzc5blFlXzE3NDc4OTg5MzM6MTc0NzkwMjUzM19WNA)

存量云主机申请IPv6地址

![img](https://ucloud-llk.feishu.cn/space/api/box/stream/download/asynccode/?code=MTI2YzBjNjRmZmI3NjkwOGI0OGZkYjExYzU0OTNkZjhfN3ZzYkpGbFloOUlLUFVEeGlVOHp5QW10UmlUWjhYcm1fVG9rZW46RkcxbWJGMUpBbzY5a2R4SGhJUWN0cHFKbm9oXzE3NDc4OTg5MzM6MTc0NzkwMjUzM19WNA)