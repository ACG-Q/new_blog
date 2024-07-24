> 前情提要: 把Hexo博客的部分文章搬运到Gmeek博客中

### 需求

1. **读取json配置文件**：包含每个issue的标题、Markdown文件路径和标签。

2. **处理**Markdown文件中的YAML头信息

   > 采取的措施是 删除

3. **检查和创建缺失的标签**：在GitHub仓库中检查标签，如果不存在则创建。

4. **创建GitHub Issue**：根据读取的标题、处理后的Markdown内容和标签创建GitHub Issue。

### 待完成

- [ ] 提取Markdown文件的YAML头信息
  - [ ] 创建时间
  - [ ] 标签 –> issue标签
  - [ ] 标题 –> issue标题
- [ ] 标签的随机RGB颜色

### 脚本实现

```python
import requests
import json
import os

# 配置你的GitHub信息
GITHUB_TOKEN = 'GitHub个人访问令牌'
REPO_OWNER = '仓库所有者'
REPO_NAME = '仓库名称'

def get_existing_labels(owner, repo):
    """获取现有标签列表"""
    url = f'https://api.github.com/repos/{owner}/{repo}/labels'
    headers = {
        'Authorization': f'token {GITHUB_TOKEN}',
        'Accept': 'application/vnd.github.v3+json'
    }
    response = requests.get(url, headers=headers)
    if response.status_code == 200:
        labels = [label['name'] for label in response.json()]
        return labels
    else:
        print(f'获取标签失败: {response.content}')
        return []

def create_label(owner, repo, label):
    """创建新的标签"""
    url = f'https://api.github.com/repos/{owner}/{repo}/labels'
    headers = {
        'Authorization': f'token {GITHUB_TOKEN}',
        'Accept': 'application/vnd.github.v3+json'
    }
    payload = {
        'name': label,
        'color': 'ffffff'  # 可以根据需要指定标签颜色
    }
    response = requests.post(url, headers=headers, data=json.dumps(payload))
    if response.status_code == 201:
        print(f'标签 "{label}" 创建成功!')
    else:
        print(f'创建标签 "{label}" 失败: {response.content}')

def create_issue(owner, repo, title, body, labels):
    """创建GitHub Issue"""
    url = f'https://api.github.com/repos/{owner}/{repo}/issues'
    headers = {
        'Authorization': f'token {GITHUB_TOKEN}',
        'Accept': 'application/vnd.github.v3+json'
    }
    payload = {
        'title': title,
        'body': body,
        'labels': labels
    }
    response = requests.post(url, headers=headers, data=json.dumps(payload))
    if response.status_code == 201:
        print(f'Issue "{title}" 创建成功!')
    else:
        print(f'创建 Issue "{title}" 失败: {response.content}')

def strip_yaml_front_matter(content):
    """去除YAML头信息并处理日期"""
    if content.startswith('---'):
        parts = content.split('---', 2)
        if len(parts) > 2:
            return parts[2].strip()
    return content

def main():
    with open('issues.json', 'r', encoding='utf-8') as f:
        issues = json.load(f)
    
    existing_labels = get_existing_labels(REPO_OWNER, REPO_NAME)
    
    for issue in issues:
        title = issue['title']
        labels = issue['labels']
        
        body_file = issue['body_file']
        if os.path.exists(body_file):
            with open(body_file, 'r', encoding='utf-8') as bf:
                body = bf.read()
                body = strip_yaml_front_matter(body)
        else:
            print(f'文件 {body_file} 不存在，跳过创建该Issue。')
            continue
        
        for label in labels:
            if label not in existing_labels:
                create_label(REPO_OWNER, REPO_NAME, label)
                existing_labels.append(label)
        
        create_issue(REPO_OWNER, REPO_NAME, title, body, labels)

if __name__ == '__main__':
    main()
```

### 使用方法

1. 在[Settings / Developer Settings](https://github.com/settings/tokens)创建Github Token

   > 推荐创建**Fine-grained personal access tokens**
   >
   > 1. 在**Repository access**中选择**Only select repositories**，并点击**Select repositories**按钮，选择Gmeek框架对应的仓库
   > 2. 在**Permissions**的**Repository permissions**中的**Issues**的下拉框中选择**Read and write**
   > 3. 然后确定

2. 修改以下配置

   ```python
   GITHUB_TOKEN = 'GitHub个人访问令牌'
   REPO_OWNER = '仓库所有者'
   REPO_NAME = '仓库名称'
   ```

   > 将 `'GitHub个人访问令牌'` 替换为你在第1步中获得的GitHub个人访问令牌，将 `'仓库所有者'` 和 `'仓库名称'` 替换为你要操作的GitHub仓库的拥有者和名称。

3. 创建`issues.json`文件

   ```json
   [
     {
       "title": "Issue 标题 1",
       "body_file": "path/to/markdown_file_1.md",
       "labels": ["标签1", "标签2"]
     },
     {
       "title": "Issue 标题 2",
       "body_file": "path/to/markdown_file_2.md",
       "labels": ["标签3", "标签4"]
     }
   ]
   ```

   | 参数        | 含义                                   |
   | ----------- | -------------------------------------- |
   | `title`     | GitHub Issue的标题                     |
   | `body_file` | 包含Markdown内容的文件路径             |
   | `labels`    | 一个字符串数组，包含GitHub Issue的标签 |

4. 运行脚本

> tip: 由于是批量上传的，会导致action的issue触发机制异常，需要后续手动触发一次action

> tip: 运行脚本时，请勿开启代理








































































































































































