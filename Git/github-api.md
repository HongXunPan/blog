

### 获取md文件

- md 源文件

```
https://raw.githubusercontent.com/HongXunPan/markdown-to-html/master/README.md
```



- html 格式

```
curl 'https://api.github.com/repos/HongXunPan/markdown-to-htmE.md' -i -H "Accept: application/vnd.github.VERSION.html"
```

### 获取 commit 信息

```
https://api.github.com/repos/HongXunPan/kangxuanpeng.com/commits?since=timestamp in ISO 8601 format: YYYY-MM-DDTHH:MM:SSZ.
```



```
https://api.github.com/repos/HongXunPan/kangxuanpeng.com/commits/39653a6177902c057b0212ade64b2aa8d93a5eed
```

获取变更的文件



```
"files": [
    {
      "sha": "09432d2e0963e74f9a25dc6db89f26c1c0e8de76",
      "filename": "Modules/Blog/Http/Controllers/PostController.php",
      "status": "modified",
      "additions": 6,
      "deletions": 0,
      "changes": 6,
      "blob_url": "https://github.com/HongXunPan/kangxuanpeng.com/blob/39653a6177902c057b0212ade64b2aa8d93a5eed/Modules/Blog/Http/Controllers/PostController.php",
      "raw_url": "https://github.com/HongXunPan/kangxuanpeng.com/raw/39653a6177902c057b0212ade64b2aa8d93a5eed/Modules/Blog/Http/Controllers/PostController.php",
      "contents_url": "https://api.github.com/repos/HongXunPan/kangxuanpeng.com/contents/Modules/Blog/Http/Controllers/PostController.php?ref=39653a6177902c057b0212ade64b2aa8d93a5eed",
      "patch": "@@ -10,9 +10,13 @@\n \n \n use App\\CommentBlog;\n+use App\\Events\\Event;\n use App\\PostBlog;\n use HyperDown\\Parser;\n use Illuminate\\Routing\\Controller;\n+use Modules\\Blog\\Events\\CommentNoticeEvent;\n+use Modules\\Blog\\Events\\NoticeCommentatorEvent;\n+use Modules\\Blog\\Mail\\NoticeCommentatorMail;\n \n class PostController extends Controller\n {\n@@ -60,6 +64,8 @@ public function comment($id)\n         $input['post_id'] = $id;\n         if (!$input['site']) unset($input['site']) ;\n         $res = CommentBlog::create($input);\n+        event(new NoticeCommentatorEvent($res));\n+        event(new CommentNoticeEvent($res));\n         return redirect($input['_'].'#li-comment-'.$res->comment_id);\n     }\n }\n\\ No newline at end of file"
    },
    {
      "sha": "3bf2c965ee5dadc6c3fd2c0a68054656f1507e1a",
      "filename": "Modules/Blog/Mail/CommentNoticeMail.php",
      "status": "added",
      "additions": 48,
      "deletions": 0,
      "changes": 48,
      "blob_url": "https://github.com/HongXunPan/kangxuanpeng.com/blob/39653a6177902c057b0212ade64b2aa8d93a5eed/Modules/Blog/Mail/CommentNoticeMail.php",
      "raw_url": "https://github.com/HongXunPan/kangxuanpeng.com/raw/39653a6177902c057b0212ade64b2aa8d93a5eed/Modules/Blog/Mail/CommentNoticeMail.php",
      "contents_url": "https://api.github.com/repos/HongXunPan/kangxuanpeng.com/contents/Modules/Blog/Mail/CommentNoticeMail.php?ref=39653a6177902c057b0212ade64b2aa8d93a5eed",
      "patch": "@@ -0,0 +1,48 @@\n+<?php\n+\n+namespace Modules\\Blog\\Mail;\n+\n+use App\\CommentBlog;\n+use App\\PostBlog;\n+use Illuminate\\Bus\\Queueable;\n+use Illuminate\\Mail\\Mailable;\n+use Illuminate\\Queue\\SerializesModels;\n+use Illuminate\\Contracts\\Queue\\ShouldQueue;\n+\n+class CommentNoticeMail extends Mailable\n+{\n+    use Queueable, SerializesModels;\n+\n+    /**\n+     * @var CommentBlog $comment\n+     */\n+    public $comment;\n+\n+    /**\n+     * @var PostBlog $post\n+     */\n+    public $post;\n+\n+    /**\n+     * Create a new message instance.\n+     *\n+     * BlogNotice constructor.\n+     * @param PostBlog $post\n+     * @param CommentBlog $comment\n+     */\n+    public function __construct(PostBlog $post, CommentBlog $comment)\n+    {\n+        $this->post = $post;\n+        $this->comment = $comment;\n+    }\n+\n+    /**\n+     * Build the message.\n+     *\n+     * @return $this\n+     */\n+    public function build()\n+    {\n+        return $this->view('blog::mail.commentNotice')->subject('文章 ‘'.$this->post->post_name.'‘ 有了新的留言');\n+    }\n+}"
    },
    {
      "sha": "e1ce7480acea21f220d489b2706ad3c4c252e54b",
      "filename": "Modules/Blog/Resources/views/mail/commentNotice.blade.php",
      "status": "added",
      "additions": 31,
      "deletions": 0,
      "changes": 31,
      "blob_url": "https://github.com/HongXunPan/kangxuanpeng.com/blob/39653a6177902c057b0212ade64b2aa8d93a5eed/Modules/Blog/Resources/views/mail/commentNotice.blade.php",
      "raw_url": "https://github.com/HongXunPan/kangxuanpeng.com/raw/39653a6177902c057b0212ade64b2aa8d93a5eed/Modules/Blog/Resources/views/mail/commentNotice.blade.php",
      "contents_url": "https://api.github.com/repos/HongXunPan/kangxuanpeng.com/contents/Modules/Blog/Resources/views/mail/commentNotice.blade.php?ref=39653a6177902c057b0212ade64b2aa8d93a5eed",
      "patch": "@@ -0,0 +1,31 @@\n+<div style=\"color:#555;font:12px/1.5 微软雅黑,Tahoma,Helvetica,Arial,sans-serif;width:650px;margin:50px auto;border-top: none;box-shadow:0 0px 3px #aaaaaa;\">\n+    <table border=\"0\" cellspacing=\"0\" cellpadding=\"0\">\n+        <tbody>\n+        <tr valign=\"top\" height=\"2\">\n+            <td valign=\"top\">\n+                <div style=\"background-color:white;border-top: 2px solid #eb5055;box-shadow: 0 1px 3px #AAAAAA;line-padding:0 15px 12px;width:650px;color:#555555;font-family:微软雅黑, Arial;font-size:12px;margin: 0 auto;\">\n+                    <h2 style=\"border-bottom:1px solid #DDD;font-size:14px;font-weight:normal;padding:8px 0 10px 8px;\"><span\n+                                style=\"color: #eb5055;font-weight: bold;\">&gt; </span>文章 <a\n+                                style=\"text-decoration:none;color: #eb5055;font-weight:600;\"\n+                                href=\"{{ url('post/').'/'.$post->post_id.'/'.$post->slug }}\">{{ $post->post_name }}</a>\n+                        有新的留言啦！</h2>\n+                    <div style=\"padding:0 12px 0 12px;margin-top:18px\">\n+                        <p>尊敬的 HongXunPan! 您的文章 《{{ $post->post_name }}》 </p>\n+                        <p>{{ $comment->nick_name }} 的留言如下:</p>\n+                        <p style=\"background-color: #EEE;border: 1px solid #DDD;padding: 20px;margin: 15px 0;\">\n+                            @if($comment->parent)\n+                                @<color>{{ $comment->parent->nick_name }}</color>\n+                            @endif\n+                            {{ $comment->content }}</p>\n+                        <p>您可以点击 <a style=\"text-decoration:none; color:#5692BC\"\n+                                    href=\"{{ url('post/').'/'.$post->post_id.'/'.$post->slug }}#comments\"\n+                                    target=\"_blank\">这里查看留言的完整內容</a>，也欢迎再次光临\n+                            <a style=\"text-decoration:none; color:#5692BC\" href=\"http://blog.kangxuanpeng.com\">HongXunPan</a>。祝您生活愉快，心想事成，欢迎下次访问！谢谢。\n+                        </p>\n+                        <p style=\"padding-bottom: 15px;\">(此邮件由系统自动发出, 请勿回复)</p></div>\n+                </div>\n+            </td>\n+        </tr>\n+        </tbody>\n+    </table>\n+</div>\n\\ No newline at end of file"
    }
  ]
```

https://developer.github.com/v3/repos/commits/

### webhook

获取commit sha

```
"head_commit": {
    "id": "2750c5314385aba94142fbb7a23f7c07eacd738c",
    "tree_id": "f843c08fa5c3f7b7d2ec9fd8dd9616df96c1056f",
    "distinct": true,
    "message": "ban email",
    "timestamp": "2019-04-20T13:31:32+08:00",
    "url": "https://github.com/HongXunPan/kangxuanpeng.com/commit/2750c5314385aba94142fbb7a23f7c07eacd738c",
    "author": {
      "name": "HongXunPan",
      "email": "15889325915@163.com",
      "username": "HongXunPan"
    },
    "committer": {
      "name": "HongXunPan",
      "email": "15889325915@163.com",
      "username": "HongXunPan"
    },
    "added": [

    ],
    "removed": [

    ],
    "modified": [
      "Modules/Blog/Http/Controllers/PostController.php"
    ]
  }
```



## 参考资料

- [将 Markdown 渲染交给 GitHub](https://ukn.me/jiang-markdown-xuan-ran-jiao-gei-github.html)

- [一篇文章搞定Github API 调用 (v3）](https://segmentfault.com/a/1190000015144126?utm_source=tag-newest)