FROM ubuntu:22.04

# 必要なツールを一括インストール
RUN apt update && apt install -y \
  gcc-arm-none-eabi \
  gdb-multiarch \
  cmake \
  git \
  build-essential

# 作業ディレクトリを設定
WORKDIR /workspace