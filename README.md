
# rn0.39填坑


1.  Android版本下Text lineHeight必须为int型
2.  ios版本图片不再支持borderTopLeftRadius等四个角的设定，VIew还支持
3.  image 的resizeMode 模式如何使用


# android填坑

>主要记录在Android开发过程中遇到的问题,解决方案，以及引发的思考。

## 目录

- [x] [需求分析](###  需求分析)
- [x] 系统设计
- [x] 详细设计
- [ ] 编码
- [ ] 测试
- [ ] 交付
- [ ] app如何进行崩溃日志收集？
- [ ] 研究内存泄漏分析
- [ ] Android版本，二维码生成后存储，没有获得权限，如何获得权限
- [ ] sdk>=18,如何使用系统自带的图库，进行图片多选？
- [ ] 图片浏览时，为什么内存占用不释放？
- [ ] fresco是如何优化图片内存的？
- [ ] android目前最优秀的裁切工具ucrop，是不是得研究下源码？
- [ ] android Intent Action 大全
- [x] [android studio 如何打包library并上传本地仓库，如何上传网络仓库](### android studio 如何打包library并上传本地仓库，如何上传网络仓库)

## 正文

### android studio 如何打包library并上传本地仓库，如何上传网络仓库

使用文件管理library，显得既低效又混乱，考虑使用gradle，把librar打包之后上传本地maven库获得网络库。


1. 本地库


2. 网络库


###  需求分析





# MIT License

Copyright (c) 2017 hammercui

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.














