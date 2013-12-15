---
layout: post
title: "[WP8开发] 在wp8中如何实现富文本显示（图文混排）？"
date: 2013-03-21 15:12
comments: true
categories: [wp8,富文本]
---

一个简单的场景：一条feeds里面包含了文本，QQ表情，还有超链接，和图片。如何展示。

这里需要用到一个控件叫做[RickTextBlock(System.Windows.Controls.RickTextBlock)](http://msdn.microsoft.com/zh-cn/library/system.windows.controls.richtextblock%28v=vs.95%29.aspx)。这个控件在工具箱里面找不到，但是可以XAML里面直接手写输入得到。用法比较简单，我直接上代码了。我这里把文本、图片和链接封装了一下，方便调用。代码如下：

<!--more -->

XAML:

    <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <RichTextBox x:Name="RickTextBox">
            
        </RichTextBox>
    </Grid>

C#:


	public partial class MainPage : PhoneApplicationPage {
        // 构造函数
        public MainPage() {
            InitializeComponent();
            Paragraph p = new Paragraph();
            List<MyBlock > BlockList = new List< MyBlock>();
            BlockList.Add( new MyTextBlock ("今天天气不错!！今天天气不错!！今天天气不错!！今天天气不错!！" , Colors.LightGray));
            BlockList.Add( new ImageBlock("http://ctc.qzs.qq.com/qzone/em/e113.png" ));
            BlockList.Add( new UrlBlock("http://user.qzone.qq.com/312315220" , "点击进入我的空间"));
            foreach (MyBlock block in BlockList) {
                p.Inlines.Add(block.getInline());
            }
            RickTextBox.Blocks.Add(p);
        }
    }

    public class MyBlock {
        public virtual Inline getInline() {
            return null ;
        }
    }

    /// <summary>
    /// 超链接
    /// </summary>
    public class UrlBlock : MyBlock {
        private string _url;
        private string _text;
        public UrlBlock(string url, string text) {
            _url = url;
            _text = text;
        }
        public override Inline getInline() {
            Hyperlink hl = new Hyperlink();
            hl.Foreground = new SolidColorBrush (Colors.Blue);
            hl.TargetName = "ExternalWebTask" + DateTime.Now.Millisecond;
            hl.Click += HyperlinkButton_Click;
            hl.Inlines.Add(System.Net. HttpUtility.UrlDecode(_text));
            hl.NavigateUri = new Uri(System.Net.HttpUtility .UrlDecode(_url), UriKind.Absolute);
            return hl;
        }


        private void HyperlinkButton_Click(object sender, RoutedEventArgs e) {
            if (e == null && e.OriginalSource as Hyperlink == null) {
                return;
            }
            if ((e.OriginalSource as Hyperlink).NavigateUri == null) {
                return;
            }
            WebBrowserTask task = new WebBrowserTask();
            task.Uri = (e.OriginalSource as Hyperlink ).NavigateUri;
            try {
                task.Show();
            } catch (Exception ex) {
                System.Diagnostics. Debug.WriteLine(ex.ToString());
            }
        }
    }


    /// <summary>
    /// 图片
    /// </summary>
    public class ImageBlock : MyBlock {
        private string _emoUrl;
        public ImageBlock(string url) {
            _emoUrl = url;
        }


        public override Inline getInline() {
            Image image = new Image();
            BitmapImage bm = new BitmapImage();
            Uri emoUri = new Uri(_emoUrl, UriKind.Absolute);
            image.Source = new BitmapImage (emoUri);
            image.Width = 24;
            image.Height = 24;
            InlineUIContainer iuc = new InlineUIContainer();
            iuc.Child = image;
            return iuc;
        }
    }

    /// <summary>
    /// 纯文字
    /// </summary>
    public class MyTextBlock : MyBlock {
        private string _text;
        private Color _color;
        public MyTextBlock(string text) {
            _text = text;
        }
        public MyTextBlock(string text, Color color) {
            _text = text;
            _color = color;
        }
        public override Inline getInline() {
            Run r = new Run();
            if (_color.ToString() != "#00000000" ) {
                r.Foreground = new SolidColorBrush (_color);
            }
            r.Text = _text;
            r.FontSize = 20;
            return r;
        }
    }


富文本展示demo:[点击下载附件](/plugin/rich-textblock-demo.rar)
