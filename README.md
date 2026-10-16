# Grok Imagine Image API (`grok-imagine-image`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **from $0.016 per image** on the APIMart route — `https://api.apimart.ai/v1` OpenAI 호환 엔드포인트 하나면 되고, 월 구독 플랜은 필요 없습니다. *(2026-10-12 기준)*

**[API 키](https://go.apimart.ai/k-69bd9d)** · **[가격](https://go.apimart.ai/k-6d0f07)** · **[모델 페이지](https://go.apimart.ai/k-65c836)** · [⚡ 60초 퀵스타트](#quickstart)

**팀들이 APIMart를 통해 Grok Imagine Image(`grok-imagine-image`)을 쓰는 이유**

- **키 하나로 카탈로그 전체를.** 동일한 `https://api.apimart.ai/v1` base URL과 `Authorization` 헤더로 Grok Imagine Image(`grok-imagine-image`)과 이미지·비디오·언어 모델 300+ 개에 접근할 수 있습니다. 바꾸는 건 `model` 필드뿐이고 클라이언트는 그대로입니다.
- **최소 $1, 종량제.** 구독도, 사전에 규모를 정해야 하는 선불 플랜도 없습니다. $1부터 충전해 호출한 만큼만 쓰면 됩니다.
- **과금 내역은 응답으로 돌아옵니다.** 완료된 모든 작업은 청구 금액(`cost` / `credits_cost`)을 보고하고, `failed`로 표시된 작업은 환불됩니다.
- **비동기 설계.** 제출하고 `task_id`를 받아 `GET /v1/tasks/{id}`를 폴링하면 끝입니다. 배치와 재시도는 별도 통합이 아니라 평범한 큐 작업입니다.

<!-- /conv-kit:v1 -->

**다른 이름:** 그록 이매진

The Grok Imagine API is xAI's Grok image API for powerful image generation and editing. Create stunning photorealistic images from text prompts with grok-imagine-image, or intelligently edit existing images with natural language using grok-imagine-image.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Per-image | `grok-imagine-image` | per delivered image, by resolution | see the [model page](https://go.apimart.ai/k-65c836) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/images/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| 1K | 0.02 | 0.016 |
| 2K | 0.02 | 0.016 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-6d0f07) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | what to generate, or what to change when editing |
| `size` | string | `auto` / `1:1` | aspect ratio, or `auto` where the route supports it |
| `resolution` | string | `1K` | output resolution tier — cost scales with it |
| `n` | integer | `1` | number of images per task, where the route allows more than one |
| `image_urls` | string[] | — | reference images as public HTTP(S) URLs or base64 data URLs |
| `nsfw_check` | boolean | `false` | runs `omni-moderation-latest` before submitting |

The table lists the fields accepted by the APIMart `images/generations` endpoint. Check the [model page](https://go.apimart.ai/k-65c836) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"grok-imagine-image","prompt":"A bamboo forest at sunrise, slow push-in","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "grok-imagine-image", "prompt": "A bamboo forest at sunrise, slow push-in",
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["images"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Grok Imagine Image 모델 id는 무엇인가요?**

`grok-imagine-image`입니다. 위의 라우트 표를 참고하세요.

**과금 방식은 어떻게 되나요?**

**from $0.016 per image** on the APIMart route. 완료된 작업은 `cost` 필드에 정확한 금액을 보고하고, 실패한 작업은 환불됩니다.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-69bd9d) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [grok-image-prompts-ko](https://github.com/dgihsnwb08756/grok-image-prompts-ko)
- **Same model, other languages** — 9 more language builds: 中文 `grok-image-api-cn` · Deutsch [grok-image-api-de](https://github.com/o10sh4cma/grok-image-api-de) · Español [grok-image-api-es](https://github.com/hjp-rba2usc/grok-image-api-es) · Français `grok-image-api-fr` · Relay/gateway [grok-image-api-gateway](https://github.com/apimarttyaxy/grok-image-api-gateway) · Bahasa [grok-image-api-id](https://github.com/b09l-tuh/grok-image-api-id) · 日本語 [grok-image-api-ja](https://github.com/i0hziz/grok-image-api-ja) · Português [grok-image-api-pt](https://github.com/bhaiiya1p6r32/grok-image-api-pt) · Русский [grok-image-api-ru](https://github.com/xpu7s80na/grok-image-api-ru)
- **More models in this language** — [midjourney-api-ko](https://github.com/ocvewn2-9ueo/midjourney-api-ko) · [gemini-2-5-flash-image-preview-api-ko](https://github.com/uiyflzgo/gemini-2-5-flash-image-preview-api-ko) · [nano-banana-api-ko](https://github.com/l-2jb09sq/nano-banana-api-ko) · [gemini-3-1-flash-image-preview-api-ko](https://github.com/sgc3a85h2-33l/gemini-3-1-flash-image-preview-api-ko) · [nano-banana-2-api-ko](https://github.com/jjb18r/nano-banana-2-api-ko) · [gemini-3-pro-image-preview-api-ko](https://github.com/a-me4/gemini-3-pro-image-preview-api-ko)
- **Topic hubs** — every build of this model, all languages: [grok-image](https://github.com/topics/grok-image) · [grok-image-api](https://github.com/topics/grok-image-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-65c836> | `apimart.ai/ko/model/grok-image` |
| Live pricing | <https://go.apimart.ai/k-6d0f07> | `apimart.ai/ko/pricing` |
| Get an API key | <https://go.apimart.ai/k-69bd9d> | `apimart.ai/ko/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Grok Imagine Image is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
