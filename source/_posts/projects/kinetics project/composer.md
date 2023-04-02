---
title: Composer代码问题记录
date: 2023.3.2
tags: 
- composer
- 问题记录
categories:
- 群体行为识别
- 问题记录
---

## 问题记录

**代码实现有误**

`tnt_four_scales_with_ball`

```python
left_group_people_repre = person_feats_thisbatch_proj.flatten(0,1)[left_group_people_idx.flatten(0,1)].view(B, self.num_person_per_group, -1)  # (B, N/2, d)
```

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302172044883.png" alt="image-20230217204428854" style="zoom:50%;" />

原代码中的方法不能正确提取数据。

正解如下图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302172043420.png" alt="image-20230217204345392" style="zoom:50%;" />