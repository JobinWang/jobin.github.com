---
layout: post
title: 关于Ruby的时间
---

### TODO：主要内容

* UTC & GMT
* Time
* Datetime
* timecop
  Timecop.freeze(24.hours.from_now)
* Timezone gem?

ActiveSupport::TimeZone.new("").formatted_offset
                               .local(*args)

now.in_time_zone('')

now.change


