---
title: mixin
created: 2023-07-26
date: 2023-07-31
tags:
- entity
- dev-ops
---

CFW 的 mixin 可以实现在机场基础上进一步制定规则。  
由于有对更方便的使用 ChatGPT 的需求，不希望频繁切换节点，可以通过 mixin 实现

```javascript
module.exports.parse = ({ content }) => {
  const mustHaveKeywords = ['美国', 'United States']
  // 过滤掉美国节点中，包含以下关键字的节点（低质量节点）,每一项为正则，忽略大小写
  const mustNotHaveKeywords = ['实验性', '0\\.', 'b']

  const regexParts = []
  mustHaveKeywords.forEach(keyword => {
    const mustNotHavePart = mustNotHaveKeywords.map(k => `(?!.*${k})`).join('')
    regexParts.push(`(?=.*${keyword}${mustNotHavePart}).*`)
  })
  const regex = new RegExp(`^(${regexParts.join('|')})$`, 'i')

  const rules = [
    'DOMAIN-KEYWORD,cloudfare,ChatGPT',
    'DOMAIN-KEYWORD,openai,ChatGPT',
    'DOMAIN-KEYWORD,sentry,ChatGPT',
    'DOMAIN-SUFFIX,ai.com,ChatGPT',
    'DOMAIN-SUFFIX,auth0.com,ChatGPT',
    'DOMAIN-SUFFIX,challenges.cloudflare.com,ChatGPT',
    'DOMAIN-SUFFIX,client-api.arkoselabs.com,ChatGPT',
    'DOMAIN-SUFFIX,events.statsigapi.net,ChatGPT',
    'DOMAIN-SUFFIX,featuregates.org,ChatGPT',
    'DOMAIN-SUFFIX,identrust.com,ChatGPT',
    'DOMAIN-SUFFIX,ingest.sentry.io,ChatGPT',
    'DOMAIN-SUFFIX,intercom.io,ChatGPT',
    'DOMAIN-SUFFIX,intercomcdn.com,ChatGPT',
    'DOMAIN-SUFFIX,openai.com,ChatGPT',
    'DOMAIN-SUFFIX,openaiapi-site.azureedge.net,ChatGPT',
    'DOMAIN-SUFFIX,stripe.com,ChatGPT',
  ]
  const proxies = content.proxies
    .filter(e => regex.test(e.name))
    .map(e => e.name)
  const groups = content['proxy-groups']

  const newGroup = {
    name: 'ChatGPT',
    type: 'select',
    proxies,
  }

  if (groups.length > 1) {
    groups.splice(1, 0, newGroup)
    content.rules = rules.concat(content.rules)
  }
  return content
}
```

## References

1. <https://github.com/Fndroid/clash_for_windows_pkg/issues/4568>
1. <https://github.com/JeffersonQin/clash-multi-mixin>