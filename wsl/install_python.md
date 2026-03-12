# Install Python in WSL

Start by [installing uv](https://docs.astral.sh/uv/getting-started/installation/).

```zsh
curl -LsSf https://astral.sh/uv/install.sh | sh
echo 'eval "$(uv generate-shell-completion zsh)"' >> ~/.zshrc
omz reload
```

Create a virtual environment.

- This would typically be done per-project within the project folder.
  - For per-project, either do not make the alias, or make it generic to activate venv from the working directing
  - `alias activate-venv='source .venv/bin/activate'`.
- But making a "global" venv for use across projects, just as an example.
- Note that Snowflake Python connector does not support newest Python, so using 3.12.

```zsh
mkdir ~/venvs/python312
cd ~/venvs/python312
uv venv --python 3.12
```

Add a shortcut alias to activate that venv

```zsh
nano ~/.zshrc
# add the following to the end of the file
alias venv-312='source ~/venvs/python3_12/.venv/bin/activate'
# save and close (Ctrl+S, Ctrl+X)
omz reload
```

Now activate that venv with `venv-312`.
Zsh should show the venv name before the command line.

Install packages in to the venv.

```zsh
# make sure the venv is active first
# this should show the bin from the venv folder
which python

# install the packages
# change the examples as needed
uv pip install dbt-core dbt-snowflake

# specifically for dbt-core, need to restart shell then reactive the venv then verify it
# should be 1.something (fusion is 2, not Python)
omz reload
venv-312
dbt --version
```
