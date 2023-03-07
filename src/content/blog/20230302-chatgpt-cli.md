---
pubDatetime: 2023-03-02T16:00:00Z
title: ChatGPT-cli
postSlug: chatgpt-cli
featured: true
draft: false
tags:
  - project
ogImage: ""
description: Recently, OpenAI released API for ChatGPT, I took advantage of that, and did a little test run. This is going to be a fairly short blog post, all I've done is to test the new API.
---

Recently, OpenAI released API for ChatGPT, I took advantage of that, and did a little test run. This is going to be a fairly short blog post, all I've done is to test the new API.

## API

According to [OpenAI's API](https://platform.openai.com/docs/guides/chat/introduction), a call for a ChatGPT response using Python is in the following format:

```python
import openai

openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Who won the world series in 2020?"},
        {"role": "assistant", "content": "The Los Angeles Dodgers won the World Series in 2020."},
        {"role": "user", "content": "Where was it played?"}
    ]
)
```

Here, `gpt-3.5-turbo` is ChatGPT. `messages` stores the whole conversation, including the initial prompt in the role of `system`, user's questions in the role of `user`, and ChatGPT's response in the role of `assistant`. The `create` function returns a response like this:

```
{
 'id': 'chatcmpl-6p9XYPYSTTRi0xEviKjjilqrWU2Ve',
 'object': 'chat.completion',
 'created': 1677649420,
 'model': 'gpt-3.5-turbo',
 'usage': {'prompt_tokens': 56, 'completion_tokens': 31, 'total_tokens': 87},
 'choices': [
   {
    'message': {
      'role': 'assistant',
      'content': 'The 2020 World Series was played in Arlington, Texas at the Globe Life Field, which was the new home stadium for the Texas Rangers.'},
    'finish_reason': 'stop',
    'index': 0
   }
  ]
}
```

Which, we really only care about the content most of the time.

## Implementation

First of all, I get my OpenAI API key. Then, I simply wrote a while loop, at the beginning of each iteration it asks for a user input. If user types !Q, then the program quits. If user types !SAVE, program saves this session's entire conversation for record or for future use. I have both raw version and formatted version.

```python
while True:
    user_input = input(user_header)
    # exit condition
    if user_input == "!Q":
        return
    # promp save
    if user_input == "!SAVE":
        parsed_messages = []
        for i in messages:
            parsed_messages.append(f"{i['role']}: {i['content']}\n")

        now = datetime.datetime.now()
        timestamp = now.strftime("%Y-%m-%d-%H-%M-%S")
        try:
            # save raw messages
            with open(file=f'C:\code\chatGPT\log\{timestamp}.txt', mode='w', encoding='utf-8') as f:
                f.write(str(messages))
            #save formatted messages
            with open(file=f'C:\code\chatGPT\log\\f-{timestamp}.txt', mode='w', encoding='utf-8') as f:
                for i in parsed_messages:
                    f.write(i)
        except IOError:
            print("Error: Could not create file.")
        print(GPT_header + 'Your file has been saved! Let me know if you need any further assistance.')
        continue
```

Note that `split_string()` is a function I wrote solely for the purpose of readability of text. Also, `GPT_header` is a colored string achieved using `colorama`.

As for handling the `messages` part, I first initiated a list called `messages` outside of the while loop, then within the while loop, I appended both the user's prompt and ChatGPT's answer to `messages`. As for getting the responses, I followed the documentations.

```python
messages.append({"role": "user", "content": user_input})

response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=messages
)

print(GPT_header + response['choices'][0]['message']['content'])

messages.append({"role": "assistant", "content": response['choices'][0]['message']['content']})
```

Oh, and finally, as a cherry on top, I created a `.bat` file named `gpt.bat` under the `\scripts` folder of Python, because this folder is included in `PATH` of my computer, and now I can simply type `gpt` in terminal to run my script.

```bat
@echo off
python "C:\path\to\my\script.py" %*
```

## Demo

![img1](https://i3.lensdump.com/i/TBTjCq.png)

![img2](https://i1.lensdump.com/i/TBTWhD.png)

![img3](https://i.lensdump.com/i/TBTAY0.png)

## Conclusion

This project alone is pretty much useless, because with everything you can do on the cli program, you can do it on the official website. However, in the future, I might implement some more interesting programs, such as a foreign language tutor, a personal assistant, a story teller and so on.
