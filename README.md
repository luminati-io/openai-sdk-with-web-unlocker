# How to integrate OpenAI Agents SDK with a Web Unlocker for High Performance

[![Bright Data Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/)

This guide explains how to create robust AI agents in Python by combining OpenAI's Agents SDK with Web Unlocker API to retrieve and process data from websites.

- [What Is OpenAI Agents SDK?](#what-is-openai-agents-sdk)
- [Major Challenges with This AI Agent Approach](#major-challenges-with-this-ai-agent-approach)
- [Integrating Agents SDK with a Web Unlocker API](#integrating-agents-sdk-with-a-web-unlocker-api)
  - [Step #1: Project Setup](#step-1-project-setup)
  - [Step #2: Install the Project's Dependencies and Get Started](#step-2-install-the-projects-dependencies-and-get-started)
  - [Step #3: Set Up Environment Variables Reading](#step-3-set-up-environment-variables-reading)
  - [Step #4: Set Up OpenAI Agents SDK](#step-4-set-up-openai-agents-sdk)
  - [Step #5: Set Up Web Unlocker API](#step-5-set-up-web-unlocker-api)
  - [Step #6: Create the Web Page Content Extraction Function](#step-6-create-the-web-page-content-extraction-function)
  - [Step #7: Define the Data Models](#step-7-define-the-data-models)
  - [Step #8: Initialize the Agent logic](#step-8-initialize-the-agent-logic)
  - [Step #9: Implement the Execution Loop](#step-9-implement-the-execution-loop)
  - [Step #10: Put It All Together](#step-10-put-it-all-together)
  - [Step #11: Test the AI Agent](#step-11-test-the-ai-agent)

## What Is OpenAI Agents SDK?

The [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) is an open-source Python library created by OpenAI. It enables developers to build agent-based AI applications in a straightforward, efficient, and production-ready manner. This library represents a refined version of OpenAI's earlier experimental project called [Swarm](https://github.com/openai/swarm).

The OpenAI Agents SDK provides several essential components with minimal abstraction:

- **Agents**: LLMs coupled with specific instructions and tools to execute tasks
- **Handoffs**: Enabling agents to transfer tasks to other agents when necessary
- **Guardrails**: To verify agent inputs to ensure they conform to expected formats or requirements

These core elements, combined with Python's versatility, facilitate the creation of sophisticated interactions between agents and tools.

The SDK also features built-in tracing capabilities, allowing you to visualize, troubleshoot, and assess your agent workflows. It even supports model fine-tuning for your particular use cases.

## Major Challenges with This AI Agent Approach

Most AI agents aim to automate operations on web pages, whether extracting content or interacting with page elements. Essentially, they need to programmatically navigate the Web.

Beyond potential misinterpretations from the AI model itself, the most significant [obstacle these agents encounter](https://brightdata.com/blog/web-data/anti-scraping-techniques) is dealing with websites' defensive mechanisms. This occurs because many sites implement anti-bot and anti-scraping technologies that can restrict or misdirect AI agents. This is particularly relevant today, as [anti-AI CAPTCHAs and sophisticated bot detection systems become increasingly prevalent](https://hackernoon.com/ai-agent-browsers-are-failing-and-its-not-just-because-of-captchas).

To overcome these obstacles, you need to enhance your agent's web navigation capabilities by integrating it with a solution like [Bright Data's Web Unlocker API](https://brightdata.com/products/web-unlocker). This tool works with any HTTP client or solution that connects to the Internet (including AI agents), serving as a web-unlocking gateway. It provides clean, unblocked HTML from any webpage. No more CAPTCHAs, IP restrictions, or inaccessible content.

## Integrating Agents SDK with a Web Unlocker API

In this guided section, you'll discover how to integrate the OpenAI Agents SDK with Bright Data's Web Unlocker API to construct an AI agent capable of:

1. Creating summaries of text from any web page
2. Obtaining structured product information from e-commerce websites
3. Collecting key details from news articles

To accomplish this, the agent will instruct the OpenAI Agents SDK to utilize the Web Unlocker API as a mechanism for obtaining the content of any web page. Once the content is acquired, the agent will apply AI logic to extract and format the data as required for each task.

> **Disclaimer**:
> 
> The three use cases mentioned above are merely examples. The methodology presented here can be extended to numerous other scenarios by customizing the agent's behavior.

Follow these instructions to develop an AI scraping agent in Python using the OpenAI Agents SDK and Bright Data's Web Unlocker API for optimal performance.

### Prerequisites

Before starting this tutorial, ensure you have the following:

- Python 3 or higher installed on your computer
- An active Bright Data account
- An active OpenAI account
- A fundamental understanding of HTTP requests
- Some familiarity with Pydantic models
- A general understanding of AI agent functionality

### Step #1: Project Setup

First, verify that Python 3 is installed on your system. If not, [download Python](https://www.python.org/downloads/) and follow the installation instructions for your operating system.

Launch your terminal and create a new directory for your scraping agent project:

```sh
mkdir openai-sdk-agent
```

The `openai-sdk-agent` directory will house all the code for your Python-based, Agents SDK-powered agent.

Move into the project directory and establish a [virtual environment](https://docs.python.org/3/library/venv.html):

```sh
cd openai-sdk-agent
python -m venv venv
```

Open the project directory in your preferred Python IDE. [Visual Studio Code with the Python extension](https://code.visualstudio.com/docs/languages/python) or [PyCharm Community Edition](https://www.jetbrains.com/pycharm/download/#section=windows) are excellent options.

Within the `openai-sdk-agent` directory, create a new Python file named `agent.py`. Your directory structure should now appear as follows:

![The file structure of the AI agent project](https://media.brightdata.com/2025/04/The-file-structure-of-the-AI-agent-project.png)

Currently, `scraper.py` is an empty Python script, but it will soon contain the desired AI agent logic.

In the IDE's terminal, activate the virtual environment. For Linux or macOS, execute this command:

```sh
./env/bin/activate
```

Similarly, on Windows, run:

```powershell
env/Scripts/activate
```

### Step #2: Install the Project's Dependencies and Get Started

This project utilizes the following Python libraries:

- [`openai-agents`](https://openai.github.io/openai-agents-python/): The OpenAI Agents SDK, used for creating AI agents in Python.
- [`requests`](https://requests.readthedocs.io/en/latest/): For connecting to Bright Data's Web Unlocker API and retrieving the HTML content of a web page for the AI agent to process. Learn more in our guide on [mastering the Python Requests library](https://brightdata.com/blog/web-data/python-requests-guide).
- [`pydantic`](https://docs.pydantic.dev/latest/): For defining structured output models, allowing the agent to return data in a clear and validated format.
- [`markdownify`](https://python.langchain.com/docs/integrations/document_transformers/markdownify/): For converting raw HTML content into clean Markdown. (We'll explain the benefits of this shortly.)
- [`python-dotenv`](https://github.com/theskumar/python-dotenv): For loading environment variables from a `.env` file. This is where we'll store credentials for OpenAI and Bright Data.

In an activated virtual environment, install them all with:

```sh
pip install requests pydantic openai-agents openai-agents markdownify python-dotenv
```

Now, set up `scraper.py` with these imports and async boilerplate code:

```python
import asyncio
from agents import Agent, RunResult, Runner, function_tool
import requests
from pydantic import BaseModel
from markdownify import markdownify as md
from dotenv import load_dotenv 

# AI agent logic...

async def run():
    # Call the async AI agent logic...

if __name__ == "__main__":
    asyncio.run(run())
```

### Step #3: Set Up Environment Variables Reading

Create a `.env` file in your project directory:

![Adding a .env file to your project](https://media.brightdata.com/2025/04/Adding-a-.env-file-to-your-project.png)

This file will store your environment variables, such as API keys and secret tokens. To load the environment variables from the `.env` file, use `load_dotenv()` from the `dotenv` package:

```python
load_dotenv()
```

You can now access specific environment variables using [`os.getenv()`](https://docs.python.org/3/library/os.html#os.getenv) like this:

```python
os.getenv("ENV_NAME")
```

Remember to import [`os`](https://docs.python.org/3/library/os.html) from the Python standard library:

```python
import os
```

### Step #4: Set Up OpenAI Agents SDK

You need a valid OpenAI API key to use the OpenAI Agents SDK. If you haven't generated one yet, follow [OpenAI's official guide](https://help.openai.com/en/articles/4936850-where-do-i-find-my-openai-api-key) to create your API key.

After obtaining it, add the key to your `.env` file like this:

```python
OPENAI_API_KEY="<YOUR_OPENAI_KEY>"
```

Make sure to replace the `<YOUR_OPENAI_KEY>` placeholder with your actual key.

No additional setup is necessary, as the `openai-agents` SDK is designed to automatically retrieve the API key from the `OPENAI_API_KEY` environment variable.

### Step #5: Set Up Web Unlocker API

If you don't already have one, [create a Bright Data account](https://brightdata.com/?hs_signup=1). Otherwise, simply [log in.](https://brightdata.com/cp/start)

Next, consult [Bright Data's official Web Unlocker documentation](https://docs.brightdata.com/scraping-automation/web-unlocker/quickstart) to obtain your API token. Alternatively, follow these steps.

In your Bright Data "User Dashboard" page, select the "Get proxy products" option:

![Clicking the "Get proxy products" option](https://media.brightdata.com/2025/04/Clicking-the-Get-proxy-products-option.png)

In the products table, find the row labeled "unblocker" and click on it:

![Clicking the "unblocker" row](https://media.brightdata.com/2025/04/Clicking-the-unblocker-row.png)

On the "unlocker" page, copy your API token using the clipboard icon:

![Copying the API token](https://media.brightdata.com/2025/04/Copying-the-API-token.png)

Also, verify that the toggle in the top-right corner is switched to "On," indicating that the Web Unlocker product is active.

Under the "Configuration" tab, make sure these options are enabled for optimal effectiveness:

- [Premium domains](https://docs.brightdata.com/scraping-automation/web-unlocker/configuration#premium-domains)
- [CAPTCHA Solver](https://brightdata.com/products/web-unlocker/captcha-solver)

![Making sure that the premium options for effectiveness are enabled](https://media.brightdata.com/2025/04/Making-sure-that-the-premium-options-for-effectiveness-are-enabled.png)

In the `.env` file, add this environment variable:

```python
BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN="<YOUR_BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN>"
```

Replace the placeholder with your actual API token.

### Step #6: Create the Web Page Content Extraction Function

Create a `get_page_content()` function that:

1. Reads the `BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN` environment variable
2. Uses `requests` to [send a request to Bright Data's Web Unlocker API using the provided URL](https://docs.brightdata.com/scraping-automation/web-unlocker/send-your-first-request)
3. Retrieves the raw HTML returned by the API
4. Transforms the HTML to Markdown and returns it

Implement the above logic as follows:

```python
@function_tool
def get_page_content(url: str) -> str:
    """
    Retrieves the HTML content of a given web page using Bright Data's Web Unlocker API,
    bypassing anti-bot protections. The response is converted from raw HTML to Markdown
    for easier and cheaper processing.

    Args:
        url (str): The URL of the web page to scrape.

    Returns:
        str: The Markdown-formatted content of the requested page.
    """

    # Read the Bright Data's Web Unlocker API token from the envs
    BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN = os.getenv("BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN")

    # Configure the Web Unlocker API call
    api_url = "https://api.brightdata.com/request"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN}"
    }
    data = {
        "zone": "unblocker",
        "url": url,
        "format": "raw"
    }

    # Make the call to Web Uncloker to retrieve the unblocked HTML of the target page
    response = requests.post(api_url, headers=headers, data=json.dumps(data))

    # Extract the raw HTML response
    html = response.text

    # Convert the HTML to markdown and return it
    markdown_text = md(html)

    return markdown_text
```

**Note 1**: The function must be annotated with [`@function_tool`](https://openai.github.io/openai-agents-python/tools/). This special decorator informs the OpenAI Agents SDK that this function can be used as a tool by an agent to perform specific actions. In this case, the function serves as the "engine" the agent can utilize to retrieve the content of the web page it will process.

**Note 2**: The `get_page_content()` function must explicitly declare the input types.  
If you omit them, you'll encounter an error like: `Error getting response: Error code: 400 - {'error': {'message': "Invalid schema for function 'get_page_content': In context=('properties', 'url'), schema must have a 'type' key.``"`

Converting raw HTML to Markdown improves performance efficiency and cost-effectiveness because HTML is highly verbose and often contains unnecessary elements like scripts, styles, and metadata. AI agents don't need this content. If your agent only requires the essentials like text, links, and images, Markdown provides a much cleaner and more compact representation.

Specifically, the HTML-to-Markdown transformation can reduce the input size by up to 99%, saving both:

- Tokens, which reduces costs when using OpenAI models
- Processing time, since models operate faster on smaller inputs

For more insights, read the article "[Why Are the New AI Agents Choosing Markdown Over HTML?](https://hackernoon.com/why-are-the-new-ai-agents-choosing-markdown-over-html)"

### Step #7: Define the Data Models

For proper operation, OpenAI SDK agents require Pydantic models to define the expected structure of their output data. Remember that the agent we're building can return one of three possible outputs:

1.  A summary of the page
2.  Product information
3.  News article information

Let's define three corresponding [Pydantic models](https://docs.pydantic.dev/latest/concepts/models/):

```python
class Summary(BaseModel):
    summary: str

class Product(BaseModel):
    name: str
    price: Optional[float] = None
    currency: Optional[str] = None
    ratings: Optional[int] = None
    rating_score: Optional[float] = None

class News(BaseModel):
    title: str
    subtitle: Optional[str] = None
    authors: Optional[List[str]] = None
    text: str
    publication_date: Optional[str] = None
```

**Note**: Using `Optional` makes your agent more versatile and general-purpose. Not all pages will include every piece of data defined in the schema, so this flexibility helps prevent errors when fields are missing.

Don't forget to import `Optional` and `List` from [`typing`](https://docs.python.org/3/library/typing.html):

```python
from typing import Optional, List
```

### Step #8: Initialize the Agent logic

Use the [`Agent`](https://platform.openai.com/docs/guides/agents) class from the `openai-agents` SDK to define the three specialized agents:

```python
summarization_agent = Agent(
    name="Text Summarization Agent",
    instructions="You are a content summarization agent that summarizes the input text.",
    tools=[get_page_content],
    output_type=Summary,
)

product_info_agent = Agent(
    name="Product Information Agent",
    instructions="You are a product parsing agent that extracts product details from text.",
    tools=[get_page_content],
    output_type=Product,
)

news_info_agent = Agent(
    name="News Information Agent",
    instructions="You are a news parsing agent that extracts relevant news details from text.",
    tools=[get_page_content],
    output_type=News,
)
```

Each agent:

1. Contains a clear instruction string that describes its intended function. The OpenAI Agents SDK uses this to guide the agent's behavior.
2. Uses `get_page_content()` as a tool to retrieve the input data (i.e., the content of the web page).
3. Returns its output in one of the Pydantic models (`Summary`, `Product`, or `News`) defined earlier.

To automatically direct user requests to the appropriate specialized agent, define a higher-level agent:

```python
routing_agent = Agent(
    name="Routing Agent",
    instructions=(
        "You are a high-level decision-making agent. Based on the user's request, "
        "hand off the task to the appropriate agent."
    ),
    handoffs=[summarization_agent, product_info_agent, news_info_agent],
) 
```

This is the agent you'll query in your `run()` function to drive the AI agent logic.

### Step #9: Implement the Execution Loop

In the `run()` function, add this loop to launch your AI agent logic:

```python
# Keep iterating until the use type "exit"
while True:
    # Read the user's request
    request = input("Your request -> ")
    # Stops the execution if the user types "exit"
    if request.lower() in ["exit"]:
        print("Exiting the agent...")
        break
    # Read the page URL to operate on
    url = input("Page URL -> ")

    # Routing the user's request to the right agent
    output = await Runner.run(routing_agent, input=f"{request} {url}")
    # Conver the agent's output to a JSON string
    json_output = json.dumps(output.final_output.model_dump(), indent=4)
    print(f"Output -> \n{json_output}\n\n")
```

This loop continuously monitors for user input and processes each request by routing it to the appropriate agent (summary, product, or news). It combines the user's query with the target URL, executes the logic, and then displays the structured result in JSON format using [`json`](https://docs.python.org/3/library/json.html). Import it with:

```python
import json
```

### Step #10: Put It All Together

Your `scraper.py` file should now contain:

```python
import asyncio
from agents import Agent, RunResult, Runner, function_tool
import requests
from pydantic import BaseModel
from markdownify import markdownify as md
from dotenv import load_dotenv
import os
from typing import Optional, List
import json

# Load the environment variables from the .env file
load_dotenv()

# Define the Pydantic output models for your AI agent
class Summary(BaseModel):
    summary: str

class Product(BaseModel):
    name: str
    price: Optional[float] = None
    currency: Optional[str] = None
    ratings: Optional[int] = None
    rating_score: Optional[float] = None

class News(BaseModel):
    title: str
    subtitle: Optional[str] = None
    authors: Optional[List[str]] = None
    text: str
    publication_date: Optional[str] = None

@function_tool
def get_page_content(url: str) -> str:
    """
    Retrieves the HTML content of a given web page using Bright Data's Web Unlocker API,
    bypassing anti-bot protections. The response is converted from raw HTML to Markdown
    for easier and cheaper processing.

    Args:
        url (str): The URL of the web page to scrape.

    Returns:
        str: The Markdown-formatted content of the requested page.
    """

    # Read the Bright Data's Web Unlocker API token from the envs
    BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN = os.getenv("BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN")

    # Configure the Web Unlocker API call
    api_url = "https://api.brightdata.com/request"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {BRIGHT_DATA_WEB_UNLOCKER_API_TOKEN}"
    }
    data = {
        "zone": "unblocker",
        "url": url,
        "format": "raw"
    }

    # Make the call to Web Uncloker to retrieve the unblocked HTML of the target page
    response = requests.post(api_url, headers=headers, data=json.dumps(data))

    # Extract the raw HTML response
    html = response.text

    # Convert the HTML to markdown and return it
    markdown_text = md(html)

    return markdown_text

# Define the individual OpenAI agents
summarization_agent = Agent(
    name="Text Summarization Agent",
    instructions="You are a content summarization agent that summarizes the input text.",
    tools=[get_page_content],
    output_type=Summary,
)

product_info_agent = Agent(
    name="Product Information Agent",
    instructions="You are a product parsing agent that extracts product details from text.",
    tools=[get_page_content],
    output_type=Product,
)

news_info_agent = Agent(
    name="News Information Agent",
    instructions="You are a news parsing agent that extracts relevant news details from text.",
    tools=[get_page_content],
    output_type=News,
)

# Define a high-level routing agent that delegates tasks to the appropriate specialized agent
routing_agent = Agent(
    name="Routing Agent",
    instructions=(
        "You are a high-level decision-making agent. Based on the user's request, "
        "hand off the task to the appropriate agent."
    ),
    handoffs=[summarization_agent, product_info_agent, news_info_agent],
)

async def run():
    # Keep iterating until the use type "exit"
    while True:
        # Read the user's request
        request = input("Your request -> ")
        # Stops the execution if the user types "exit"
        if request.lower() in ["exit"]:
            print("Exiting the agent...")
            break
        # Read the page URL to operate on
        url = input("Page URL -> ")

        # Routing the user's request to the right agent
        output = await Runner.run(routing_agent, input=f"{request} {url}")
        # Conver the agent's output to a JSON string
        json_output = json.dumps(output.final_output.model_dump(), indent=4)
        print(f"Output -> \n{json_output}\n\n")


if __name__ == "__main__":
    asyncio.run(run())
```

### Step #11: Test the AI Agent

To launch your AI agent, execute:

```sh
python agent.py
```

Let's say you want to summarize the content from [Bright Data's AI services hub](https://brightdata.com/ai). Simply enter a request like this:

![The input to get a summary of Bright Data's AI services](https://media.brightdata.com/2025/04/The-input-to-get-a-summary-of-Bright-Datas-AI-services.png)

Here's the JSON-formatted result you'll receive:

![The summary returned by your AI agent](https://media.brightdata.com/2025/04/The-summary-returned-by-your-AI-agent.png)

Now, imagine you want to extract product data from an Amazon product page, such as the [PS5 listing](https://www.amazon.com/PlayStation%C2%AE5-console-slim-PlayStation-5/dp/B0CL61F39H/):

![The Amazon PS5 page](https://media.brightdata.com/2025/04/The-Amazon-PS5-page.png)

Typically, [Amazon's CAPTCHA and anti-bot systems](https://brightdata.com/blog/web-data/bypass-amazon-captcha) would block your request. With the Web Unlocker API, your AI agent can access and analyze the page without being blocked:

![Getting Amazon product data](https://media.brightdata.com/2025/04/Getting-Amazon-product-data.gif)

The output will be:

```json
{
    "name": "PlayStation\u00ae5 console (slim)",
    "price": 499.0,
    "currency": "USD",
    "ratings": 6321,
    "rating_score": 4.7
}
```

Finally, suppose you want to get structured news information from [a Yahoo News article](https://www.yahoo.com/news/pope-francis-dies-88-080859417.html):

![The target Yahoo News article](https://media.brightdata.com/2025/04/The-target-Yahoo-News-article.png)

Accomplish this with the following input:

```
Your request -> Give me news info
Page URL -> https://www.yahoo.com/news/pope-francis-dies-88-080859417.html
```

The result will be:

```json
{
    "title": "Pope Francis Dies at 88",
    "subtitle": null,
    "authors": [
        "Nick Vivarelli",
        "Wilson Chapman"
    ],
    "text": "Pope Francis, the 266th Catholic Church leader who tried to position the church to be more inclusive, died on Easter Monday, Vatican officials confirmed. He was 88. (omitted for brevity...)",
    "publication_date": "Mon, April 21, 2025 at 8:08 AM UTC"
}
```

## Conclusion

Combining the OpenAI SDK with Bright Data's [Web Unlocker API](https://brightdata.com/products/web-unlocker) enables you to develop AI agents that can reliably operate on virtually any web page. This is just one example of how Bright Data's products and services can support advanced AI integrations.

Explore our complete range of [AI products](https://brightdata.com/ai/products-for-ai): autonomous AI agents, vertical AI apps, foundation models, multimodal AI, data providers, data packages, and more.

Create a Bright Data account and try all our products and services for AI agent development today!