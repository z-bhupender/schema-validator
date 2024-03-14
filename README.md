# schema-validator
Problem Statement : Validation Process that allows us to maintain the schema of the .json and .yaml file on the GitHub Repository so that we do not make mistakes while any config changes.

Solution: Use GitHub Actions Workflows for file schema validation whenever a pull request is raised.

Steps Involved:

1. Taking a example of dataset_schema.yaml file which is the exact replica of our scorecard.Note: Few skill removed for demo purpose only.
    ```
    ---
    version: _constant(2.0,string)
    call_id: _meta(call_id)
    contact_id: _meta(contact_id)
    cat1: _meta(cat1)
    cat2: _meta(cat2)
    agent_name: _meta(agent_name)
    agentid: _meta(agentid)
    supervisor_pk: _meta(supervisor_pk)
    agent_pk: _meta(agent_pk)
    calldirection: _meta(calldirection)
    supervisor_user_name: _meta(supervisor_user_name)
    agent_user_name: _meta(agent_user_name)
    timestamp: _meta(starttime)
    base_path: _meta(base_path)
    file_path: _meta(file_path)
    calltype: _gpt(libs/dev/config/ally/rules/gpt_rules/templates_2/configuration.json,calltype)
    metadata:
      model_details:
        calltype:
          version: _constant(9,string)
          name: _constant(v9,string)
          description: _constant(Using GPT Single Pass, string)
          date: _constant(2023-12-13,string)
          dataset:
            version: _constant(0,string)
            description: _constant(No Dataset, string)
        rules.negotiation_flow.unwilling.probing_questions:
          version: _constant(7.3,string)
          name: _constant(v7.3,string)
          description:
            _constant(Using ZenNLU with old Luis Rules and regex and .r parser
            rules, string)
          date: _constant(2022-12-21,string)
          dataset:
            version: _constant(5,string)
            description:
              _constant(intent labeled on each transcripts and customized training
              dataset for luis app, string)
        rules.negotiation_flow.unwilling.other_income:
          version: _constant(9,string)
          name: _constant(v9,string)
          description: _constant(Using GPT Single Pass, string)
          date: _constant(2023-12-13,string)
          dataset:
            version: _constant(0,string)
            description: _constant(No Dataset, string)
    scores:
      rules.negotiation_flow.unwilling.probing_questions:
        AI_Scores:
          score: _rulepath(libs/dev/config/ally/rules/ally_negotiation_flow_unwilling_probing_questions.r=>ally_negotiation_flow_unwilling_probing_questions, int)
          score_date: _datenow(%m-%d-%Y %H:%M:%S)
          matching_transcripts:
            _rulepath(libs/dev/config/ally/rules/ally_negotiation_flow_unwilling_probing_questions.r=>ally_negotiation_flow_unwilling_probing_questions,
            transcript)
      rules.negotiation_flow.unwilling.other_income: _gpt(libs/dev/config/ally/rules/gpt_rules/templates_3/configuration_proc_1197_2.json, rules.negotiation_flow.vague.other_income)
    ```
    This is the file we actually wants to be maintained, But what is the issues that while making changes sometimes we might add a symbol or any other characters or might rename a key by mistake and while reviewing the pull request these kind of mistakes just slip into the main code so and while running pipelines we face these issue and again making new pull request to correct these.

2. Now point is that how can we prevent this from happening. For this we can use schema validator that is triggered when a pull request is raised with the help of GitHub Actions Workflow.

   Steps included to set that up:
   
   i. Create a file `.github/workflows/schema_validator.yaml`
    ```
    ---
    name: Schema Validator
    
    on:
      pull_request:
        branches:
          - main
          - beta
    
    jobs:
      validate-schema:
        runs-on: ubuntu-latest
    
        steps:
        - name: Checkout Code
          uses: actions/checkout@v2
    
        - name: Install jsonschema
          run: pip install jsonschema
    
        - name: Validate YAML Schema
          run: |
            python -c 'import jsonschema, yaml; schema = yaml.safe_load(open("dataset_schema_validator.yaml")); data = yaml.safe_load(open("dataset_schema.yaml")); jsonschema.validate(data, schema)'
    ```
   ii. Then add the schema for a particular file in this case for dataset_schema.yaml this is the schema with file named dataset_schema_validator.yaml

    ```
    ---
    type: object
    properties:
      version:
        type: string
        const: "_constant(2.0,string)"
      call_id:
        type: string
        const: "_meta(call_id)"
      contact_id:
        type: string
        const: "_meta(contact_id)"
      cat1:
        type: string
        const: "_meta(cat1)"
      cat2:
        type: string
        const: "_meta(cat2)"
      agent_name:
        type: string
        const: "_meta(agent_name)"
      agentid:
        type: string
        const: "_meta(agentid)"
      supervisor_pk:
        type: string
        const: "_meta(supervisor_pk)"
      agent_pk:
        type: string
        const: "_meta(agent_pk)"
      calldirection:
        type: string
        const: "_meta(calldirection)"
      supervisor_user_name:
        type: string
        const: "_meta(supervisor_user_name)"
      agent_user_name:
        type: string
        const: "_meta(agent_user_name)"
      timestamp:
        type: string
        const: "_meta(starttime)"
      base_path:
        type: string
        const: "_meta(base_path)"
      file_path:
        type: string
        const: "_meta(file_path)"
      calltype:
        type: string
        const: "_gpt(libs/dev/config/ally/rules/gpt_rules/templates_2/configuration.json,calltype)"
      metadata:
        type: object
        properties:
          model_details:
            type: object
            properties:
              calltype:
                type: object
                properties:
                  version:
                    type: string
                    const: "_constant(9,string)"
                  name:
                    type: string
                    const: "_constant(v9,string)"
                  description:
                    type: string
                    const: "_constant(Using GPT Single Pass, string)"
                  date:
                    type: string
                    const: "_constant(2023-12-13,string)"
                  dataset:
                    type: object
                    properties:
                      version:
                        type: string
                        const: "_constant(0,string)"
                      description:
                        type: string
                        const: "_constant(No Dataset, string)"
              rules.negotiation_flow.unwilling.probing_questions:
                type: object
                properties:
                  version:
                    type: string
                    const: "_constant(7.3,string)"
                  name:
                    type: string
                    const: "_constant(v7.3,string)"
                  description:
                    type: string
                    const: "_constant(Using ZenNLU with old Luis Rules and regex and .r parser
                      rules, string)"
                  date:
                    type: string
                    const: "_constant(2022-12-21,string)"
                  dataset:
                    type: object
                    properties:
                      version:
                        type: string
                        const: "_constant(5,string)"
                      description:
                        type: string
                        const: "_constant(intent labeled on each transcripts and customized training
                          dataset for luis app, string)"
              rules.negotiation_flow.unwilling.other_income:
                type: object
                properties:
                  version:
                    type: string
                    const: "_constant(9,string)"
                  name:
                    type: string
                    const: "_constant(v9,string)"
                  description:
                    type: string
                    const: "_constant(Using GPT Single Pass, string)"
                  date:
                    type: string
                    const: "_constant(2023-12-13,string)"
                  dataset:
                    type: object
                    properties:
                      version:
                        type: string
                        const: "_constant(0,string)"
                      description:
                        type: string
                        const: "_constant(No Dataset, string)"
      scores:
        type: object
        properties:
          rules.negotiation_flow.unwilling.probing_questions:
            type: object
            properties:
              score:
                type: string
                const: "_rulepath(libs/dev/config/ally/rules/ally_negotiation_flow_unwilling_probing_questions.r=>ally_negotiation_flow_unwilling_probing_questions, int)"
              score_date:
                type: string
                const: "_datenow(%m-%d-%Y %H:%M:%S)"
              matching_transcripts:
                type: string
                const: "_rulepath(libs/dev/config/ally/rules/ally_negotiation_flow_unwilling_probing_questions.r=>ally_negotiation_flow_unwilling_probing_questions,
                  transcript)"
          rules.negotiation_flow.unwilling.other_income:
            type: string
            const: "_gpt(libs/dev/config/ally/rules/gpt_rules/templates_3/configuration_proc_1197_2.json, rules.negotiation_flow.vague.other_income)"
    ```


3. Whenever we want to add a new file with schema just put those at the bottom of `.github/workflows/schema_validator.yaml`
