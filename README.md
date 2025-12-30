# Tobatools-config-file
## 简体中文
### 项目介绍
本项目为拖把工具箱分区配置文件的标准化规范，采用纯文本极简格式编写，无冗余嵌套语法、无额外配置项、无格式约束。遵循安卓原生底层刷机逻辑与模式权限规则，适配全品牌安卓机型，专为刷机包制作者、设备适配者设计，配置文件可直接用于刷机脚本调用，具备通用性、规范性、稳定性。本规范所有规则均为安卓官方硬性要求，无自定义魔改内容，所有操作指令均经过真机验证，可有效规避刷机报错、分区操作无效、设备变砖等问题。

### 核心规范-全局通用硬性规则
1. 配置文件首行固定为设备标识，格式为：device:机型代号，无空格、无引号，为必写项。
2. 模式声明为独立行，代表后续所有指令均在该模式下执行，仅支持几个固定值：bootloader、fastbootd、system，无其他拓展模式。
3. 所有分区操作、刷机指令，必须以英文减号「-」为前缀，单行单指令，指令格式统一无例外。
4. 注释内容以英文井号「#」为前缀，独立行编写，注释内容不参与执行，仅作说明使用，非必写项。
5. 空行仅作为内容分段使用，不影响执行逻辑，非必写项，可按需添加。
6. 配置文件编码格式为UTF-8，为强制要求，无兼容格式。

### 文件格式与命名规范
1. 文件后缀：使用.txt。
2. 文件命名规则：MI6.txt、PGP110.txt等等。
3. 文件存储：默认为根目录，你需要将镜像放在images文件夹中。

### 完整指令语法手册-全强制规则
#### 一、分区刷写指令
- 单槽刷写：-分区名，适用于无AB分区的安卓设备，为基础刷写格式。
- 双槽刷写：-分区名_ab，适用于支持AB分区架构的安卓设备，为新增标识，无其他变体格式。
- 适用范围：所有物理分区、逻辑分区的镜像刷写操作均遵循此规则。
- 示例：-recovery、-modem_ab、-boot_a、-system。

#### 二、逻辑分区专用操作指令
- 核心强制要求：逻辑分区的删除、创建操作，仅可在fastbootd模式下执行，bootloader模式完全不支持该类操作，执行即报错，无任何例外情况。
- 删除指令：-逻辑分区名 del，作用为清除指定逻辑分区及该分区的COW残留数据，为刷机前置必要操作，无需声明_cow。
- 创建指令：-逻辑分区名 add 1M，作用为重新创建指定逻辑分区，固定初始化大小为1M，安卓原生规则，刷写镜像时分区将自动扩容至镜像实际大小，无需修改数值。
- 配套规则：逻辑分区的删除指令与创建指令，需为一一对应关系，分区名称、编写顺序完全一致，删除数量与创建数量无差值。
- 示例：-my_bigball del、-my_bigball add 1M、-system del、-system add 1M。

#### 三、AVB校验禁用指令
- 指令格式：-分区名_ab disable，仅适用于vbmeta系列分区，作用为刷写分区时附加禁用AVB2校验的参数。
- 适用分区：vbmeta、vbmeta_system、vbmeta_vendor，其他分区添加该标识无实际作用。
- 适配规则：刷写官方原厂固件时，移除disable标识即可，格式为-分区名_ab。
- 示例：-vbmeta_ab disable、-vbmeta_system_ab disable、-vbmeta_vendor_ab disable。

#### 四、收尾固定指令
- 重启指令：system，作用为刷机操作完成后重启设备进入系统，为可选项。

### 模式权限硬性规则-全机型通用
bootloader模式（原生fastboot）支持操作：所有物理分区的刷写、基带分区刷写、清除数据操作、重启/模式切换操作、AVB校验禁用操作；不支持操作：逻辑分区的删除操作、逻辑分区的创建操作，执行即返回错误信息，操作无效。
fastbootd模式（用户空间fastboot）支持操作：部分物理分区刷写、所有逻辑分区刷写、逻辑分区删除操作、逻辑分区创建操作、AVB校验禁用操作、重启操作；不推荐操作：基带分区刷写，易导致基带失效、信号丢失等问题（以机型实际情况为准）。
核心适配原则：逻辑分区的删除与创建，为fastbootd模式独占操作，无任何替代方案，所有安卓机型均遵循此规则。
补充说明：小米/红米/POCO系列设备的bootloader为魔改增强版本，仅额外支持逻辑分区的刷写操作，仍不支持逻辑分区的删除与创建，该类操作仍需切换至fastbootd模式执行。OPPO、一加、真我、ViVO、华为全系设备严格遵循原生规则，无魔改内容。

### 完整配置文件标准示例-OP5551L1
```
device:OP5551L1

bootloader
-recovery_ab

fastbootd
-abl_ab
-aop_ab
-aop_config
-devcfg_ab
-dtbo_ab
-boot_ab
-vendor_boot_ab
-xbl_ab
-xbl_config_ab
-hyp_ab
-tz_ab
-uefi_ab
-uefisecapp_ab
-keymaster_ab
-qupfw_ab
-abl_ab
-aop_ab
-bluetooth_ab
-cpucp_ab
-dsp_ab
-featenabler_ab
-imagefv_ab
-oplusstanvbk_ab
-oplus_sec_ab
-shrm_ab
-splash_ab
-engineering_cdt_ab

-my_bigball del
-my_carrier del
-my_engineering del
-my_heytap del
-my_manifest del
-my_preload del
-my_product del
-my_region del
-my_stock del
-odm del
-odm_dlkm del
-product del
-system del
-system_ext del
-vendor del
-vendor_dlkm del

-my_bigball add 1M
-my_carrier add 1M
-my_engineering add 1M
-my_heytap add 1M
-my_manifest add 1M
-my_preload add 1M
-my_product add 1M
-my_region add 1M
-my_stock add 1M
-odm add 1M
-odm_dlkm add 1M
-product add 1M
-system add 1M
-system_ext add 1M
-vendor add 1M
-vendor_dlkm add 1M

-my_bigball
-my_carrier
-my_company
-my_engineering
-my_heytap
-my_manifest
-my_preload
-my_product
-my_region
-my_stock
-odm 
-odm_dlkm
-product
-system
-system_ext
-vendor
-vendor_dlkm

-vbmeta_ab disable
-vbmeta_system_ab disable
-vbmeta_vendor_ab disable

bootloader
-modem_ab

system
```

### 零基础机型适配步骤-通用标准流程
步骤1：修改配置文件首行的设备标识，将device:OP5551L1替换为目标机型的代号，格式不变。
步骤2：根据目标机型的硬件特性，增删分区指令行。删除目标机型无相关的分区指令，新增目标机型特有的分区指令，指令格式遵循本规范。
步骤3：根据目标机型特性调整指令标识。无AB分区的老机型，移除所有指令中的_ab标识；刷写官方固件时，移除vbmeta系列指令后的disable标识。
步骤4：确认所有逻辑分区的del与add指令均编写在fastbootd模式段内，无任何逻辑分区操作指令出现在bootloader模式段内。
适配说明：所有适配操作仅为增删行、修改标识，无需调整指令执行顺序，无需新增语法规则。

### 常见问题-FAQ
1. 问：逻辑分区删除与创建为何必须在fastbootd模式执行？
答：bootloader为安卓硬件级引导模式，仅具备物理分区的读写权限，无磁盘分区管理驱动，无法识别逻辑分区结构；fastbootd为安卓系统级刷机模式，加载完整的分区管理驱动，是安卓官方指定的唯一可执行逻辑分区删建操作的模式，为底层硬性规则，无例外。
2. 问：创建逻辑分区的大小为何固定为1M？
答：1M为安卓逻辑分区的标准初始占位大小，刷写镜像文件时，分区会自动扩容至镜像的实际大小，修改数值无实际意义，全机型通用该规则。
3. 问：基带分区为何建议在bootloader模式刷写？
答：基带分区为设备通信核心分区，在fastbootd模式下刷写易导致基带文件写入不完整，引发信号丢失、基带失效等问题，bootloader模式为基带分区的最优刷写环境，为真机验证后的通用规则。
4. 问：配置文件中的注释与空行是否会影响执行？
答：不会，注释内容以#开头，空行仅作分段，均不参与脚本执行，可按需添加或删除。

### 贡献指南
本项目接受所有合规贡献，包括机型配置文件提交、规则补充、问题修正等。所有贡献内容需遵循以下要求：保持配置文件的极简纯文本格式，不新增任何语法规则，不修改本规范的核心硬性规则，所有提交的配置文件均需经过真机验证，确保指令有效性与正确性。贡献内容提交时，需注明机型代号、适配的系统版本，便于审核与归档。

---
