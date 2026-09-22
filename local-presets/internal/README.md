# internal(内部模式)— 本机专属留档

以 `cordis.patch.yml` 为唯一事实源,本机位置:

    ~/.dsh/plugins/dsh-internal-preset/   (本地 bundle,link: 安装)

本目录仅留档,不进仓库 bundle(`dsh.bundle.patch` 只挂 `cordis.patch.yml`),
不上游 harness。

安装 / 卸载:

    pnpm dsh plugin --profile web add link:$HOME/.dsh/plugins/dsh-internal-preset
    # 卸载:从 ~/.dsh/profiles/web/package.json 的 dsh.profile.bundles 移除
    #        'dsh-internal-preset' 与 dependencies 后 dsh-restart

预设身份:id `internal`,name 内部模式,order 6;行集与 autonomous 完全一致(22 行,
含 planning、无 pangu 行),persona 为规范融合版(盘古门明示不静默、证据不足即停、
猜测只作待验证假设、记忆写入带 wing/room/tags)。
