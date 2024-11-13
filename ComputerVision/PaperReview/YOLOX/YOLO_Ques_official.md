![ota](https://miro.medium.com/v2/resize:fit:990/format:webp/1*T040dIc7iJL7Uhq_UkvdPw.png)

$$c^{fg} = c_{cls} + \alpha c_{reg} + c_{cp}$$
$$c_{ij} = L^{cls}_{ij} + \lambda L^{reg}_{ij} $$
- $c_{ij}$ means cost function
- where $\lambda$ is a 'balancing coeffcient' ? 
- $L^{cls}_{ij}$ and $L^{reg}_{ij}$ are *classification loss* and *regression loss* between gt $g_i$ and prediction $p_j$
- gt $g_i$ means the [[ground truth for the i-th]] object in your dataset.

Q1. ⁠OTA와 SimOTA의 차이가 정확하게 뭔지, Cost function의 차이인 건가?

What is Balancing coeffcient? Check the code

Q2. Anchor free detectors은 object의 중심위치를 예측하여 경계선까지의 거리를 regression한다. 논문의 Multi positives section에서 anchor free는 object의 중심 위치만 선택하고 다른 양질의 예측들은 무시를 한다고했는데, 구체적으로 어떤 양질의 예측들을 무시하는지 궁금하다.

![Dch](https://production-media.paperswithcode.com/methods/Screen_Shot_2021-08-26_at_2.55.44_PM_JVfxCw7.png)

Q3. 기존 Coupled Head 에서 발생한 문제점이 Regression 과 Classification 의 충돌이라고 하는데, 어떤 부분에서 충돌이 발생하는건지 궁금합니다. 

Q4. Backbone은 어떤 방식으로 레이어가 결정되고 개발이 되는지?

Already made lots of 