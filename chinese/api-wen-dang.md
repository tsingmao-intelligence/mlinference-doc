# API文档

前端服务API的设计理念是兼容[openai](https://platform.openai.com/docs/api-reference/chat/create)的接口协议。以下是支持的特性和接口定义，部分描述摘抄自openai官方文档：[https://platform.openai.com/docs/guides/text-generation/chat-completions-api](https://platform.openai.com/docs/guides/text-generation/chat-completions-api)

## Completion

该接口用于模型内容补全。

### Endpoint

`POST http://127.0.0.1:port/completion`

### Request Body

#### prompt (string or array, Required)

The prompt(s) to generate completions for, encoded as a string, array of strings, array of tokens, or array of token arrays.

#### echo (boolean or null, Optional)

Defaults to false. Echo back the prompt in addition to the completion

#### max\_tokens (integer or null, Optional)

Defaults to 16. The maximum number of tokens that can be generated in the completion. The token count of your prompt plus `max_tokens` cannot exceed the model's context length.

#### stop (string / array / null, Optional)

Defaults to null. Up to 4 sequences where the API will stop generating further tokens. The returned text will not contain the stop sequence.

#### stream (boolean or null, Optional)

Defaults to false. Whether to stream back partial progress. If set, tokens will be sent as data-only [server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent\_events/Using\_server-sent\_events#Event\_stream\_format) as they become available, with the stream terminated by a `data: [DONE]` message.

#### stream\_options (object or null, Optional)

Defaults to null. Options for streaming response. Only set this when you set `stream: true`.

* include\_usage (boolean, Optional)\
  If set, an additional chunk will be streamed before the `data: [DONE]` message. The `usage` field on this chunk shows the token usage statistics for the entire request, and the `choices` field will always be an empty array. All other chunks will also include a `usage` field, but with a null value.

#### temperature (number or null, Optional)

Defaults to 1. What sampling temperature to use, between 0 and 2. Higher values like 0.8 will make the output more random, while lower values like 0.2 will make it more focused and deterministic. We generally recommend altering this or `top_p` but not both.

#### top\_p (number or null, Optional)

Defaults to 1. An alternative to sampling with temperature, called nucleus sampling, where the model considers the results of the tokens with top\_p probability mass. So 0.1 means only the tokens comprising the top 10% probability mass are considered. We generally recommend altering this or `temperature` but not both.

#### do\_sample (bool, Optional, default false)

控制是否进行sample

### Returns

Returns a [#completion-object](api-wen-dang.md#completion-object "mention"), or a sequence of [#completion-object](api-wen-dang.md#completion-object "mention")s if the request is streamed.

### Completion Object

Represents a completion response from the API. Note: both the streamed and non-streamed response objects share the same shape (unlike the chat endpoint).

#### id (string)

A unique identifier for the completion.

#### choices (array)

The list of completion choices the model generated for the input prompt.

* finish\_reason (string)\
  The reason the model stopped generating tokens. This will be `stop` if the model hit a natural stop point or a provided stop sequence, `length` if the maximum number of tokens specified in the request was reached, or `content_filter(TBD?)` if content was omitted due to a flag from our content filters.
* index (integer)
* text (string)

#### created (integer)

The Unix timestamp (in seconds) of when the completion was created.

#### model (string)

The model used for completion.

#### object (string)

The object type, which is always "text\_completion"

#### usage (object)

Usage statistics for the completion request.

* completion\_tokens (integer)\
  Number of tokens in the generated completion.
* prompt\_tokens (integer)\
  Number of tokens in the prompt.
* total\_tokens (integer)\
  Total number of tokens used in the request (prompt + completion).

## Chat \[comming soon]
