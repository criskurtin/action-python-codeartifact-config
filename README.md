## CodeArtifact configuration Action for GitHub Actions

Outputs variables necessary for pip and twine repository configuration.  
  
Useful if pip is being used directly in workflow as well as inside the Dockerfile (`ARG` can be used to pass `PIP_INDEX_URL`).  
This is why the action outputs the variables for manual configuration instead of configuring the tools automatically via `aws cli`.

**Table of Contents**

<!-- toc -->

- [Usage](#usage)

<!-- tocstop -->

## Usage

```yaml
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ secrets.AWS_REGION }}

    - name: Retrieve CodeArtifact config
      id: codeartifact
      uses: criskurtin/action-python-codeartifact-config@v3
      with:
        aws-account-id: ${{ secrets.AWS_ACCOUNT_ID }}
        codeartifact-domain: some-domain
        codeartifact-repo: some-repo
        auth-token-duration: 3600

    - name: Publish package to CodeArtifact
      if: github.ref_name == 'main'
      env:
        PIP_INDEX_URL: ${{ steps.codeartifact.outputs.PIP_INDEX_URL }}
        TWINE_USERNAME: ${{ steps.codeartifact.outputs.TWINE_USERNAME }}
        TWINE_PASSWORD: ${{ steps.codeartifact.outputs.TWINE_PASSWORD }}
        TWINE_REPOSITORY_URL: ${{ steps.codeartifact.outputs.TWINE_REPOSITORY_URL }}
      run: |
        pip3 install --upgrade pip setuptools wheel twine
        python3 setup.py sdist bdist_wheel
        twine upload dist/*
```

See [action.yml](action.yml) for the full documentation for this action's inputs and outputs.
