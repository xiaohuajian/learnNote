## 关注tab

### 关注 FollowFragment 类

#### spCardAdapter

位于关注列表的顶部，其对应的recycleView 为rvSpCard；

1. 接口：api-usr/userFollowSP/dynamic

2. 类型：有三种

   | 类型 （int） | 布局文件                         | 说明                   |
   | ------------ | -------------------------------- | ---------------------- |
   | -1           | head_follow_item_sp_card_invalid | 非法类型               |
   | 1            | head_follow_item_sp_card_qa      | 答疑                   |
   | 2            | head_follow_item_sp_card         | 自习室、Live、付费Live |

   

#### headAdapter

关注用户的学习状态,  位于关注列表的顶部；

1. 接口： user-dynamic/follow-user-dynamic
2. 类型：只有一种itemType；

#### HomeAdapter 

查询学习圈关注列表数据，对应的recycleView为 rvFollow。

1. 接口：learning-circle/user-follow
2. 类型

| 类型                               | 布局                                | 说明 |
| ---------------------------------- | ----------------------------------- | ---- |
| HomeBean.TYPE_LOCATION_LIVE_ROOM   | R.layout.home_item_live_room        |      |
| HomeBean.TYPE_LOCATION_DIARY_IMAGE | R.layout.home_item_image            |      |
| HomeBean.TYPE_LOCATION_DIARY_VIDEO | R.layout.home_item_video            |      |
| HomeBean.TYPE_LOCATION_DIARY_TEXT  | R.layout.home_item_text             |      |
| HomeBean.TYPE_GROUP_DISCUSS        | R.layout.home_item_group_discussion |      |
| HomeBean.TYPE_GROUP_CLOCKIN        | R.layout.home_item_group_discussion |      |
| HomeBean.TYPE_GROUP_CONTRACT       | R.layout.home_item_group_discussion |      |
| HomeBean.TYPE_LOCATION_DECLARATION | R.layout.home_item_declaration      |      |
| TYPE_RECOMMEN_USERS                | R.layout.item_follow_recommend_head |      |
| -2                                 | R.layout.home_item_invalid          |      |



### 道友 FriendsFragment 类

#### FriendsListAdapter 

道友列表，其对应的列表是 rvFriends；

1. 接口地址 ：friend/list

2. 类型

   | 类型                             | 布局文件                       | 说明       |
   | -------------------------------- | ------------------------------ | ---------- |
   | FriendInfoBean.TYPE_EMPTY        | R.layout.view_add_friends_item | 添加道友   |
   | FriendInfoBean.TYPE_ADDED_FRIEND | R.layout.view_friends_item     | 已添加道友 |
   | FriendInfoBean.TYPE_LIVE_HAND    | R.layout.view_friends_item     | 小书童     |

##### 添加道友 FriendInfoBean.TYPE_EMPTY

添加道友会跳转到 InviteTypeActivity 类，然后有三种不同的类型进行分享。

##### 已添加道友 FriendInfoBean.TYPE_ADDED_FRIEND

点击已添加的道友会跳转到 UserHomePageActivity 界面，这个界面主要是viewPager 包含了四种内容，长视频、动态、日记、点赞；这里取一种类型来进行分析，其余的逻辑一样，可类比（选择动态）。

- 动态内容的列表方法 initDynamicView（）

- 对应的adapter 为 DynamicAdapter，其相应的类型有

  | 类型                                 | 布局文件                               | 说明 |
  | ------------------------------------ | -------------------------------------- | ---- |
  | Const.UserHomeDynamic.UNKNOW         | R.layout.item_dynamic_unknow           |      |
  | Const.UserHomeDynamic.DIARY          | R.layout.item_dynamic_daily            |      |
  | Const.UserHomeDynamic.AD_DIARY       | R.layout.item_dynamic_ad               |      |
  | Const.UserHomeDynamic.STUDY_TIMING   | R.layout.item_dynamic_timing_finish    |      |
  | Const.UserHomeDynamic.GEUUP_SLEEP    | R.layout.item_dynamic_getup            |      |
  | Const.UserHomeDynamic.GROUP_DISCUSS  | R.layout.item_dynamic_group_discussion |      |
  | Const.UserHomeDynamic.GROUP_CLOCKIN  | R.layout.item_dynamic_group_clock      |      |
  | Const.UserHomeDynamic.GROUP_CONTRACT | R.layout.item_dynamic_group_contract   |      |

##### 小书童 FriendInfoBean.TYPE_LIVE_HAND

对应的类是 LivehandActivity， 内部的列表为 rvLivehandList ， 对应的adapter 是 LiveHandAdapter ；

1. 其数据是从本地数据库获取的 shortMessageDao.queryBuilder()；

2. 其类型：

   | 类型                                                  | 布局文件                                        | 说明 |
   | ----------------------------------------------------- | ----------------------------------------------- | ---- |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_TEXT              | R.layout.recycle_item_livehand_text             |      |
   | Const.MSG_TYPE.KEFU_ARTICLE                           | R.layout.recycle_item_livehand_image            |      |
   | Const.MSG_TYPE.KEFU_NEW_VERSION                       | R.layout.recycle_item_livehand_image            |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_UNKOWN            | R.layout.recycle_item_livehand_text             |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_VIDEO             | R.layout.recycle_item_livehand_video            |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_IMAGE             | R.layout.recycle_item_livehand_image            |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_VOICE             | R.layout.recycle_item_livehand_voice            |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_BESEEN            | R.layout.recycle_item_livehand_reply_or_apllay  |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_ERROR_REPORT      | R.layout.recycle_item_livehand_reply_or_apllay  |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_ERROR_REPORT_REAL | R.layout.recycle_item_livehand_reply_or_apllay  |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_WEB               | R.layout.recycle_item_livehand_image            |      |
   | Const.MSG_TYPE.TEAM_INVITED_COMMON                    | R.layout.recycle_item_livehand_common_invited   |      |
   | Const.MSG_TYPE.TEAM_INVITED_CLOCK                     | R.layout.recycle_item_livehand_clock_invited    |      |
   | Const.MSG_TYPE.TEAM_INVITED_CONTRACT                  | R.layout.recycle_item_livehand_contract_invited |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_REPPLY            | R.layout.recycle_item_livehand_reply_or_apllay  |      |
   | Const.LIVEHAND_NOTICE_TYPE.LIVEHAND_BEHELLO           | R.layout.recycle_item_livehand_reply_or_apllay  |      |

   



#### RecommendFriendsAdapter

无道友，推荐道友，其对应的列表是 rvRecommendFriends；

1. 接口地址：learning-circle/user-recommend
2. 类型：只有一种类型，其布局文件为 R.layout.view_recommend_friends_item





