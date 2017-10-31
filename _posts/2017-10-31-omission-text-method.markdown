---
layout: post
title:  "文字截取的工具方法"
date:   2017-10-31
categories: Java
---

最近在项目中遇到的问题.简介内容太多,手机屏幕有限为了排版美观无法全部展示.就写了这么一个方法来截取内容    
主要功能是给定一个最大的长度限制maxLength,把目标文字截取到这个长度以内最近的一个标点的位置,尽量保证原文的完整
原文小于长度限制的不做处理,没有标点符号的直接按最大长度截取

源码:    

    /**
     * 长文字截取内容,在末尾添加省略号
     * @param content 原文
     * @param maxLength 截取的最大长度
     * @return 截取后的文字内容
     */
    public static String omissionText(String content, int maxLength) {
        String result = content;
        //处理文字长度
        if(maxLength > 0) {
            if (content != null && content.length() > maxLength) {
                result = content.substring(0, maxLength);
                String[] endMarks = {"！", "!", "。", ".","，", ","};
                int splitPt = 0;
                for (String endMark: endMarks) {
                    int pt = result.lastIndexOf(endMark);
                    if(pt > 0) {
                        if(pt > splitPt) {
                            splitPt = pt;
                        }
                    }
                }

                if (splitPt > 0) {
                    result = result.substring(0, splitPt);
                    result += "...";
                }
            }
        }
        else {
            return content;
        }

        return result;
    }

测试代码:    

    public static void main(String[] args) {
        System.out.println(omissionText("壹贰叁肆伍陆柒捌玖拾壹贰叁肆伍陆柒捌玖拾壹贰叁肆伍", 20));
        System.out.println(omissionText("壹贰叁肆伍陆", 10));
        System.out.println(omissionText("壹贰叁肆伍陆,柒捌玖拾.壹贰叁，肆伍陆。柒捌玖！拾壹贰。叁肆伍", 25));

    }