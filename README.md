# GitHub Actions Workflow Linter

A GitHub Action that lints GitHub Actions workflow files using actionlint with detailed error reporting and suggestions.

## Features

- 🔍 Validates workflow syntax and best practices
- 📊 Detailed error reporting with reviewdog
- 🎯 File annotations on problematic lines
- ✅ Checks for common mistakes
- 📋 Suggests improvements

## Usage

### Basic Usage

```yaml
name: Lint Workflows
on:
  pull_request:
    branches: [main]
    paths:
      - '.github/workflows/**'

jobs:
  lint:
    runs-on: ubuntu-24.04
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
      
      - name: Lint GitHub Actions Workflows
        uses: LogixDevCo/gh-linter-action@v1.0.0
```

### Lint on Every PR

```yaml
name: Lint Workflows
on:
  pull_request:

jobs:
  lint:
    runs-on: ubuntu-24.04
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
      
      - name: Lint GitHub Actions Workflows
        uses: LogixDevCo/gh-linter-action@v1.0.0
```

### With Custom Paths

```yaml
name: Lint Workflows
on:
  pull_request:
    paths:
      - '.github/workflows/**'
      - '.github/actions/**'

jobs:
  lint:
    runs-on: ubuntu-24.04
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
      
      - name: Lint GitHub Actions Workflows
        uses: LogixDevCo/gh-linter-action@v1.0.0
```

## Inputs

None - the action automatically finds and lints all workflow files.

## Outputs

This action generates:
- **File Annotations**: Inline comments on workflow files
- **Reviewdog Report**: Detailed findings in PR
- **Summary**: Overview of issues found

## Common Issues Detected

### Syntax Errors
```yaml
# ❌ Invalid
on: push
jobs:
  build
    runs-on: ubuntu-latest

# ✅ Valid
on: push
jobs:
  build:
    runs-on: ubuntu-latest
```

### Invalid Action Versions
```yaml
# ❌ Invalid
- uses: actions/checkout@master

# ✅ Valid
- uses: actions/checkout@v4
```

### Missing Required Fields
```yaml
# ❌ Invalid
jobs:
  build:
    steps:
      - run: echo "hello"

# ✅ Valid
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "hello"
```

### Shell Script Issues
```yaml
# ❌ Invalid
- run: |
    if [ ${{ github.ref }} == "main" ]

# ✅ Valid
- run: |
    if [ "${{ github.ref }}" == "main" ]
```

### Deprecated Syntax
```yaml
# ❌ Deprecated
- uses: actions/checkout@v2

# ✅ Current
- uses: actions/checkout@v4
```

## Checks Performed

### Workflow Syntax
- YAML syntax validation
- Required fields presence
- Valid job and step structure

### Action Usage
- Valid action references
- Proper version pinning
- Deprecated action detection

### Expression Syntax
- Correct `${{ }}` usage
- Valid context access
- Proper string escaping

### Shell Scripts
- ShellCheck integration
- Common script errors
- Best practices

### Security
- Pinned action versions
- Secure secret usage
- Permission settings

## Example Output

```
.github/workflows/ci.yml:15:7: "runs-on" is required [syntax-check]
.github/workflows/ci.yml:23:11: action "actions/checkout@master" should use commit SHA or version tag [action-version]
.github/workflows/ci.yml:45:15: shellcheck reported issue SC2086: Double quote to prevent globbing [shellcheck]
```

## Configuration

Create `.github/actionlint.yaml` for custom rules:

```yaml
self-hosted-runner:
  labels:
    - my-custom-runner
    - gpu-runner

config-variables:
  - MY_CUSTOM_VAR
  - ANOTHER_VAR
```

## Integration with Branch Protection

Make this a required check:
1. Go to repository Settings
2. Navigate to Branches
3. Add branch protection rule
4. Enable "Require status checks to pass"
5. Select "Lint Workflows"

## Required Permissions

```yaml
permissions:
  contents: read       # For checking out code
  pull-requests: write # For reviewdog comments (optional)
```

## Best Practices

1. **Run on workflow changes** to catch issues early
2. **Make it a required check** via branch protection
3. **Fix issues before merging** to maintain quality
4. **Use pinned versions** for actions
5. **Follow GitHub's best practices**

## Common Fixes

### Pin Action Versions
```yaml
# Before
- uses: actions/checkout@main

# After
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
```

### Quote Variables
```yaml
# Before
- run: echo ${{ github.ref }}

# After
- run: echo "${{ github.ref }}"
```

### Add Required Fields
```yaml
# Before
jobs:
  build:
    steps:
      - run: npm test

# After
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: npm test
```

## Troubleshooting

### No Issues Reported
- Verify workflow files exist
- Check file paths match pattern
- Ensure checkout step is present

### False Positives
- Add exceptions in actionlint.yaml
- Report issues to actionlint project
- Use inline comments to suppress

### Permission Errors
- Verify repository permissions
- Check token scopes
- Ensure reviewdog can comment

## Requirements

- GitHub Actions workflows in repository
- `.github/workflows/` directory
- GitHub Actions enabled

## Tools Used

- **actionlint**: Workflow linting
- **reviewdog**: Error reporting
- **ShellCheck**: Shell script validation

## License

MIT

## Author

TahaDekmak
