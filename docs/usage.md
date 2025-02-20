# Usage

The default command for this is `llm prompt` - you can use `llm` instead if you prefer.

## Executing a prompt against OpenAI

To run a prompt, streaming tokens as they come in:
```bash
llm 'Ten names for cheesecakes'
```
To disable streaming and only return the response once it has completed:
```bash
llm 'Ten names for cheesecakes' --no-stream
```
To switch from ChatGPT 3.5 (the default) to GPT-4 if you have access:
```bash
llm 'Ten names for cheesecakes' -m gpt4
```
You can use `-m 4` as an even shorter shortcut.

Pass `--model <model name>` to use a different model.

You can also send a prompt to standard input, for example:
```bash
echo 'Ten names for cheesecakes' | llm
```
Some models support options. You can pass these using `-o/--option name value` - for example, to set the temperature to 1.5 run this:

```bash
llm 'Ten names for cheesecakes' -o temperature 1.5
```

## Continuing a conversation

By default, the tool will start a new conversation each time you run it.

You can opt to continue the previous conversation by passing the `-c/--continue` option:
```bash
llm 'More names' --continue
```
This will re-send the prompts and responses for the previous conversation as part of the call to the language model. Note that this can add up quickly in terms of tokens, especially if you are using expensive models.

`--continue` will automatically use the same model as the conversation that you are continuing, even if you omit the `-m/--model` option.

To continue a conversation that is not the most recent one, use the `--cid/--conversation <id>` option:
```bash
llm 'More names' --cid 01h53zma5txeby33t1kbe3xk8q
```
You can find these conversation IDs using the `llm logs` command.

## Using with a shell

To generate a description of changes made to a Git repository since the last commit:
```bash
llm "Describe these changes: $(git diff)"
```
This pattern of using `$(command)` inside a double quoted string is a useful way to quickly assemble prompts.

## System prompts

You can use `-s/--system '...'` to set a system prompt.
```bash
llm 'SQL to calculate total sales by month' \
  --system 'You are an exaggerated sentient cheesecake that knows SQL and talks about cheesecake a lot'
```
This is useful for piping content to standard input, for example:
```bash
curl -s 'https://simonwillison.net/2023/May/15/per-interpreter-gils/' | \
  llm -s 'Suggest topics for this post as a JSON array'
```
## Listing available models

The `llm models list` command lists every model that can be used with LLM, along with any aliases:

```bash
llm models list
```
Example output:
```
OpenAI Chat: gpt-3.5-turbo (aliases: 3.5, chatgpt)
OpenAI Chat: gpt-3.5-turbo-16k (aliases: chatgpt-16k, 3.5-16k)
OpenAI Chat: gpt-4 (aliases: 4, gpt4)
OpenAI Chat: gpt-4-32k (aliases: 4-32k)
PaLM 2: chat-bison-001 (aliases: palm, palm2)
```
Add `--options` to also see documentation for the options supported by each model:
```bash
llm models list --options
```
Output:
<!-- [[[cog
from click.testing import CliRunner
import os, sys
sys._called_from_test = True
from llm.cli import cli
os.environ["LLM_USER_PATH"] = "/tmp"
result = CliRunner().invoke(cli, ["models", "list", "--options"])
del os.environ["LLM_USER_PATH"]
cog.out("```\n{}\n```".format(result.output))
]]] -->
```
OpenAI Chat: gpt-3.5-turbo (aliases: 3.5, chatgpt)
  temperature: float
    What sampling temperature to use, between 0 and 2. Higher values like
    0.8 will make the output more random, while lower values like 0.2 will
    make it more focused and deterministic.
  max_tokens: int
    Maximum number of tokens to generate.
  top_p: float
    An alternative to sampling with temperature, called nucleus sampling,
    where the model considers the results of the tokens with top_p
    probability mass. So 0.1 means only the tokens comprising the top 10%
    probability mass are considered. Recommended to use top_p or
    temperature but not both.
  frequency_penalty: float
    Number between -2.0 and 2.0. Positive values penalize new tokens based
    on their existing frequency in the text so far, decreasing the model's
    likelihood to repeat the same line verbatim.
  presence_penalty: float
    Number between -2.0 and 2.0. Positive values penalize new tokens based
    on whether they appear in the text so far, increasing the model's
    likelihood to talk about new topics.
  stop: str
    A string where the API will stop generating further tokens.
  logit_bias: dict, str
    Modify the likelihood of specified tokens appearing in the completion.
    Pass a JSON string like '{"1712":-100, "892":-100, "1489":-100}'
OpenAI Chat: gpt-3.5-turbo-16k (aliases: chatgpt-16k, 3.5-16k)
  temperature: float
  max_tokens: int
  top_p: float
  frequency_penalty: float
  presence_penalty: float
  stop: str
  logit_bias: dict, str
OpenAI Chat: gpt-4 (aliases: 4, gpt4)
  temperature: float
  max_tokens: int
  top_p: float
  frequency_penalty: float
  presence_penalty: float
  stop: str
  logit_bias: dict, str
OpenAI Chat: gpt-4-32k (aliases: 4-32k)
  temperature: float
  max_tokens: int
  top_p: float
  frequency_penalty: float
  presence_penalty: float
  stop: str
  logit_bias: dict, str

```
<!-- [[[end]]] -->

When running a prompt you can pass the full model name or any of the aliases to the `-m/--model` option:
```bash
llm -m chatgpt-16k 'As many names for cheesecakes as you can think of, with detailed descriptions'
```
Models that have been installed using plugins will be shown here as well.