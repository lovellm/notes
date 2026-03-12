# Init New Dbt Project

If using dbt-core, make sure to activate the venv it is installed to first.
Using fusion for example.

```zsh
dbtf init --project-name my_project --threads 4
# follow the prompts to set up the connection.
cd my_project
code .
```

Further Information

- [Quickstart - Manual Install](https://docs.getdbt.com/guides/manual-install?step=3)
- [Configure Your Local Environment](https://docs.getdbt.com/docs/configure-dbt-extension?version=1.12)
- [Install DBT Fusion](https://docs.getdbt.com/docs/local/install-dbt?version=2.0#installation)
- [Install DBT Core](https://docs.getdbt.com/docs/local/install-dbt?version=1.12#installation)
  - Example also available in my [WSL Pyton Docs](../wsl/install_python.md)

# Override Custom Schema Behavior

- Dbt by default puts everything in to your "target_schema".
- If you specify a custom schema, it will go to "target_schema_custom_schema",
  not just "custom_schema.
- If following standard dbt best practices, this is fine.
- But if you are unable to make schemas on the fly, this could be a problem.
- Overwrite the logic as needed.
- [This is probably what you want](https://docs.getdbt.com/docs/build/custom-schemas?version=1.12#a-built-in-alternative-pattern-for-generating-schema-names)
- [Source Code for the Above](https://github.com/dbt-labs/dbt-adapters/blob/ef0072a97b03317ddebdd34ff708a6b12810f643/dbt-adapters/src/dbt/include/global_project/macros/get_custom_name/get_custom_schema.sql)
- [Further Documentation Here](https://docs.getdbt.com/docs/build/custom-schemas?version=1.12)
