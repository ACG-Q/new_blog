> [!NOTE]
> 导入功能，未测试过

# 脚本

## 脚本描述
该脚本用于导出和导入已保存的 Wi-Fi 连接信息。通过读取 `/etc/NetworkManager/system-connections/` 目录下的 `.nmconnection` 文件，提取 Wi-Fi 的 SSID 和密码（PSK），并将其导出到一个文件中。导出的 Wi-Fi 配置信息可以随后通过脚本导入到其他设备中。

### 功能列表：
1. **导出 Wi-Fi 信息**：将当前保存的 Wi-Fi 连接的 SSID 和 PSK（密码）信息导出到 `wifi_export.txt` 文件中。
2. **导入 Wi-Fi 信息**：从 `wifi_export.txt` 文件中读取 Wi-Fi 配置信息并添加到当前系统中。

## 脚本内容

```bash
#!/bin/bash

# 导出已保存的 Wi-Fi 连接信息
export_wifi() {
  echo "导出已保存的 Wi-Fi 连接信息..."

  # 确保导出文件不存在
  rm -f wifi_export.txt

  # 遍历所有 .nmconnection 文件
  for file in /etc/NetworkManager/system-connections/*.nmconnection; do
    [[ -e "$file" ]] || { echo "未找到任何 Wi-Fi 配置文件"; exit 1; }

    # 确保文件是一个 Wi-Fi 连接配置文件
    if grep -q 'type=wifi' "$file"; then
      # 提取 SSID 和 PSK
      ssid=$(grep -oP '(?<=^ssid=).*' "$file")
      psk=$(grep -oP '(?<=^psk=).*' "$file")

      # 检查 ssid 是否非空，确保写入有效数据
      if [[ -n "$ssid" ]]; then
        echo "SSID: $ssid" >> wifi_export.txt
        if [[ -n "$psk" ]]; then
          echo "PSK: $psk" >> wifi_export.txt
        else
          echo "PSK: 无密码" >> wifi_export.txt
        fi
        echo "-----------------------------------------" >> wifi_export.txt
      fi
    fi
  done

  # 检查是否成功创建文件
  if [[ -f wifi_export.txt ]]; then
    echo "Wi-Fi 连接信息已导出到 wifi_export.txt"
  else
    echo "未找到任何 Wi-Fi 配置信息"
  fi
}


# 导入 Wi-Fi 连接信息
import_wifi() {
  if [[ ! -f wifi_export.txt ]]; then
    echo "wifi_export.txt 文件不存在，请先导出 Wi-Fi 连接信息"
    exit 1
  fi

  echo "导入 Wi-Fi 连接信息..."
  while IFS= read -r line; do
    if [[ $line =~ ^SSID: ]]; then
      ssid=$(echo "$line" | sed 's/SSID: //')
      read -r line
      psk=$(echo "$line" | sed 's/PSK: //')
      
      # 如果 PSK 为 "无密码"，则跳过密码设置
      if [[ "$psk" == "无密码" ]]; then
        nmcli connection add type wifi con-name "$ssid" ifname wlan0 ssid "$ssid"
      else
        nmcli connection add type wifi con-name "$ssid" ifname wlan0 ssid "$ssid" wifi-sec.key-mgmt wpa-psk
        nmcli connection modify "$ssid" wifi-sec.psk "$psk"
      fi
      nmcli connection modify "$ssid" connection.autoconnect yes
      echo "已添加 Wi-Fi: $ssid"
    fi
  done < wifi_export.txt
}

# 检查用户输入的选项
case "$1" in
  export)
    export_wifi
    ;;
  import)
    import_wifi
    ;;
  *)
    echo "使用方法: $0 {export|import}"
    exit 1
    ;;
esac
```

## 使用方法

### 1. 导出 Wi-Fi 信息：
运行以下命令将已保存的 Wi-Fi 连接信息导出到 `wifi_export.txt` 文件：

```bash
sudo ./wifi_manager.sh export
```

### 2. 导入 Wi-Fi 信息：
运行以下命令从 `wifi_export.txt` 中读取 Wi-Fi 信息并自动添加到当前系统：

```bash
sudo ./wifi_manager.sh import
```

### 注意事项：
- **权限问题**：该脚本需要管理员权限（`sudo`）才能访问和修改 `/etc/NetworkManager/system-connections/` 目录中的文件。
- **安全性**：导出的 `wifi_export.txt` 文件包含敏感的 Wi-Fi 密码信息，请确保它的权限设置为只有当前用户可以访问：

```bash
chmod 600 wifi_export.txt
```