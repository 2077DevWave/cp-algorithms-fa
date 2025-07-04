---
tags:
  - AI Translated
---

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
<script type="text/javascript">
  $(document).ready(function(){
    $("#previewBtn").click(function(){
      var markdown = $("#markdownInput").val();
      var URL = "https://us-central1-cp-algorithms.cloudfunctions.net/convert-markdown-mkdocs";
      var data = {"markdown": markdown};
      var refresh_script = `<scr` + `ipt>MathJax.typeset();</scr` + `ipt>`;
      $("#previewBtn").html("در حال بارگذاری...");
      $.ajax({
        url: URL,
        contentType: "application/json",
        method: 'POST',
        data: JSON.stringify(data),
        success: function(data) {
          $("#previewArea").html(data + "\n" + refresh_script);
          $("#previewBtn").html("پیش‌نمایش (Ctrl + Enter)");
        },
        error: function() {$("#previewArea").html("خطای داخلی!")}
      });
    });

    $('form').keydown(function(event) {
      if (event.ctrlKey && event.keyCode === 13) {
        $("#previewBtn").click();
      }
    })
  });
</script>
# پیش‌نمایش مقاله

<a href="contrib.html">اطلاعات برای مشارکت‌کنندگان</a>
<center>
<form>
  <textarea style="width:100%;height:300px;" id="markdownInput">
# مقاله نمونه

$$a^2 + b^2 = c^2$$

```cpp
int gcd (int a, int b) {
    if (b == 0)
        return a;
    else
        return gcd (b, a % b);
}
```</textarea>
  <br/>
  <br/>
  <button type='button' class="md-button md-button--primary" id="previewBtn">پیش‌نمایش (Ctrl + Enter)</button>
</form>
</center>
<hr/>

<div id="previewArea">
</div>
<br/>