# 练习 32：GitHub Actions 矩阵策略

## 目标

学习如何使用 GitHub Actions 矩阵策略来并行测试多个配置。

## 前置条件

- 有 GitHub 账号
- 熟悉 GitHub Actions 基础
- 有一个测试项目

## 步骤

### 1. 理解矩阵策略

矩阵策略允许你在一个工作流中并行运行多个作业，每个作业使用不同的配置。

**基本语法**：

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14, 16, 18]
        os: [ubuntu-latest, windows-latest, macos-latest]
    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm test
```

### 2. 创建基本矩阵

**示例：测试多个 Node.js 版本**：

```yaml
# .github/workflows/test-matrix.yml
name: Test Matrix

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14, 16, 18, 20]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
```

### 3. 使用多维矩阵

**示例：测试多个操作系统和 Node.js 版本**：

```yaml
# .github/workflows/test-multi-dim.yml
name: Test Multi-Dimensional Matrix

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [16, 18, 20]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
```

### 4. 使用矩阵排除

**示例：排除特定组合**：

```yaml
# .github/workflows/test-exclude.yml
name: Test Exclude

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [16, 18, 20]
        exclude:
          - os: windows-latest
            node-version: 16
          - os: macos-latest
            node-version: 16
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
```

### 5. 使用矩阵包含

**示例：包含特定配置**：

```yaml
# .github/workflows/test-include.yml
name: Test Include

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node-version: [16, 18]
        include:
          - os: ubuntu-latest
            node-version: 20
            experimental: true
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
        continue-on-error: ${{ matrix.experimental || false }}
```

### 6. 使用矩阵变量

**示例：使用矩阵变量**：

```yaml
# .github/workflows/test-variables.yml
name: Test Variables

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        include:
          - os: ubuntu-latest
            node-version: 18
            python-version: '3.9'
          - os: windows-latest
            node-version: 18
            python-version: '3.9'
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - name: Setup Python ${{ matrix.python-version }}
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}
      
      - name: Install Node.js dependencies
        run: npm ci
      
      - name: Install Python dependencies
        run: pip install -r requirements.txt
      
      - name: Run tests
        run: |
          npm test
          python -m pytest
```

### 7. 使用矩阵进行部署

**示例：部署到多个环境**：

```yaml
# .github/workflows/deploy-matrix.yml
name: Deploy Matrix

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        environment: [staging, production]
        include:
          - environment: staging
            url: https://staging.example.com
          - environment: production
            url: https://example.com
    environment: ${{ matrix.environment }}
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Deploy to ${{ matrix.environment }}
        run: |
          echo "Deploying to ${{ matrix.environment }}"
          echo "URL: ${{ matrix.url }}"
          # 部署命令
```

### 8. 使用矩阵进行代码质量检查

**示例：运行多个代码质量工具**：

```yaml
# .github/workflows/quality-matrix.yml
name: Quality Matrix

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        tool: [eslint, prettier, stylelint]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run ${{ matrix.tool }}
        run: npx ${{ matrix.tool }} .
```

### 9. 使用矩阵进行安全扫描

**示例：运行多个安全扫描工具**：

```yaml
# .github/workflows/security-matrix.yml
name: Security Matrix

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  security:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        tool: [trivy, snyk, sonarqube]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Run ${{ matrix.tool }}
        uses: ${{ matrix.tool }}-action@v1
        with:
          # 工具特定配置
```

### 10. 使用矩阵进行性能测试

**示例：运行多个性能测试**：

```yaml
# .github/workflows/performance-matrix.yml
name: Performance Matrix

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  performance:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        test: [load, stress, endurance]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run ${{ matrix.test }} test
        run: npm run test:${{ matrix.test }}
```

## 挑战

1. **挑战 1**：创建一个矩阵策略，测试多个 Python 版本
2. **挑战 2**：创建一个矩阵策略，部署到多个环境
3. **挑战 3**：创建一个矩阵策略，运行多个安全扫描工具
4. **挑战 4**：创建一个矩阵策略，进行性能测试
5. **挑战 5**：创建一个矩阵策略，进行代码质量检查

## 思考

1. 矩阵策略如何提高 CI/CD 效率？
2. 如何平衡矩阵的全面性和执行时间？
3. 矩阵策略的局限性是什么？
4. 如何优化矩阵策略的成本？

## 相关资源

- [GitHub Actions 矩阵策略文档](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs)
- [GitHub Actions 工作流语法](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [GitHub Actions 最佳实践](https://docs.github.com/en/actions/learn-github-actions/security-hardening-for-github-actions)

---

**上一篇：[练习 31：GitHub Copilot 高级使用](exercise-31-github-copilot-advanced.md) | 下一篇：[练习 33：GitHub API 集成](exercise-33-github-api-integration.md)**