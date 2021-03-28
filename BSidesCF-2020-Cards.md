---
title: '[BSidesCF 2020]Cards'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: SteamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-03-13 00:19:11
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1136461.jpg
---

这两天有别的事 只能勉强更新buu了。
15号以后恢复。

本题是一个21点游戏

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210313002633.png)

这种游戏题大概是要达到一定分数才会给flag。

这里要用到一个21点的规则：开牌如果直接是21点的话 则直接判胜：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210313003324.png)

所以只要写个脚本一直发包就行了：

    import requests
    import json
    
    url = "http://20383ca3-5d70-4954-b646-5ef60d4a6a33.node3.buuoj.cn/api/deal"
    SecretState = "100bbef0db059e0c55e1386e8dfcfd026c0424d03429980c091c58ea81dc5e4e753d306d4e42eb1f966a1518c729dae6155894982afe4318066021227987e3f87b4fe2dce76c2bed02efa14b26231cca0bf8ba6b6c8e55a884d33b1696122544c516111addd4d555e9f12f66263dcc1b8eb60b465a4dff96645f352b5dc7c63bfe39cc5cb566c14d481b8e0f2108b7db7b168855cc11849b32fd7bf653a1fe3c89042580a791a4cac4d6d70c7c4a285c5ecf7e7856f4f5af1d476507a7a78cf10d258ac368248e5db2f53d7846fcdef7e5dfa7688a3a1b4232a4d0932848fc6956811f82642b1f8e210fc9d837d82b21875aa13dfabfb2ca2b7cd3e0caf1419338ee5f9db148ca8708dcd0f4c1d844ffc6eed5216615df0b8a7272105d68c0aefa7440601f708548ea103053aed8e1307525f33f0c1c4bb8ebc4ca31077fa6a43ea3e319cc2037f23283ede78697cb8d04e669233f4de55dcd445afcf6eccb4151efa762ee932cb5c8ba1882f1d5733fbbbaf549de3caaa43fac28fac201636e9037d7dec28e3a2a0c798a179d45b4e1ea03cb41ee4ec76a237a9c750fb4e91bb02a96042be0979fd98d70bfca9821fb0f382d726ebeb40d179ed182bf3e41e6b8f5d1c22963915447f5cfd3085f5c0165404f7f88523f2db704b161413e77c42af18958152343d3cff392b346bd7496485493c97ea81f683179566b9b31f5895d40a9283ab611bdba55327f45e43e3a36f1ad0abafe4602342b5d5cbc221d43044d013c28fee507a79095666f95dc1362c462d21ba96b679acfe4da457465bb9066afa09dafc360dbf21e2dc9df28c47fe59c81f97f430b55d70e3c3993655659185502ddc6c477a7a77dbcf19ce188a9b99f8b8426a8043eef080ce380719def894249cd632cb28066a7d0fe8802a26a432679c50c3c9c02cd254ee247a12074b38dadd263db6713ec9bc8aa01160d698ce28b1bd2cd34fef9e1ddff5aed5f7a7585d16abf8dd86cf7b51b6654edba85fe55fbd0a13ca2d2c63514fe9082be99f26bb03bb62ae52a3c4e2dc115b0272f491609f416ba8263403a8791f15205e7db2bfee699ebe2a26ac8501c4671ad3ef865d3cf8c9e276cddbd24a0d24b0e76db2143a3d1695eed38f0c71c2372dc1f933edd9be68251edd3431236e472036fe523c1f5670abfb3e8ae3c50c38bdc82244ffc4e47d6c12d7caf4f331d95adae33e881865a84e121c8d22969aed291a4b26c7213cc07b99aedbc2f1668a513aa9d58fecc6ebdcbe4dccb8b730cd9219a050ba5eafb07b4ab0eb01f9e62e9720126303f56791113fd54431f316ac2de3d31e40a808baf432279c01ef60e5d5eb8f8bcf214316447205c64bb44699a1242d21fb2dfac0459679180a72b6cb85ed9a2ef265b5e809454fbfb02acf2ea5c17de34924089c7641d7be7553cf45ac565a4967668b834c788ffd5f90a8b3b2e482303150f87db2702fc099a35835bec50dafd5002753310c105a2dc47230931d812f39dca1f1707caada23b308b7debb08f544c71eb3d6b69c26c8e4c3005a31c41697d8900c7f0f9337d0c87a8b07d58cce3431d8cb92b280e82b8423889fdbe58d05a5b7f103719bac8def29a9e2677c7e8dd83389b9faac525c8badb870143b29051e2a8a472b349b18a2fc0542de088793fbd4686883997"
    while 1:
        data = {"Bet": 500, "SecretState": SecretState}
        r = requests.post(url=url,data=json.dumps(data)).json()
        if r['GameState'] == 'Blackjack':
            SecretState = r['SecretState']
        print(r['Balance'])
        if r['Balance'] > 100000:
            print(r)
            break

简单题。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210313010119.png)

