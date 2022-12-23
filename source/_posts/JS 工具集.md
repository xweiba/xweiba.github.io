---
title: JS工具集
date: 2019-08-08 13:47:41
tags:
 - JS
 - 工具
categories:
 - JS
---

# CommUtils.js
js 工具集合: 
```js

/**
 * 工具类
 */
"use strict";
var CommUtils = window.CommUtils = {
    //字符串动态替换 
    // CommUtils.formatStr("{0}不能为{1}", "分数", "0" -> "分数不能为0"
	formatStr : function(){ 
		var args = Array.prototype.slice.apply(arguments);
		var that = this;
		if (args.length == 0) {
			return null;
		} else if (args.length == 1) {
			return args[0];
		} else {
			var template = args[0];
			args.splice(0,1);
			if (typeof args[0] == 'object') {
				$.each(args[0], function(key, value) {
					template = template.replace(new RegExp("{" + key + "}", "g"), that.isEmpty(value) ? '' : value);
				})
			} else {
				$.each(args, function(key, value) {
					template = template.replace(eval("/({)" + key + "(})/g"), that.isEmpty(value) ? '' : value);
				})
			}
			return template;
		}
	},
	// layer tip 封装
	// CommUtils.initTip(); // 先载入jq扩展
	// $("#id").tip("提示信息", top, 12000)
	initTip : function() {
		$.fn.tip = function(content, direct, time) {
			if (direct == undefined) direct = 'bottom';
			direct = ['top', 'right', 'bottom', 'left'].indexOf(direct) + 1;
			var index = layer.tips(content, this, {
				tips : [direct, '#f5c564'],
				time : time ? time : 2000,
				anim : 6,
				tipsMore : true
			});
			if (direct == 3) {
				$('.layui-layer-tips[times="' + index + '"] .layui-layer-content').css('top', '-8px');
			} else if (direct == 2) {
				$('.layui-layer-tips[times="' + index + '"] .layui-layer-content').css('left', '-8px');
			} else if (direct == 1) {
				$('.layui-layer-tips[times="' + index + '"] .layui-layer-content').css('top', '8px');
			} else if (direct == 4) {
				$('.layui-layer-tips[times="' + index + '"] .layui-layer-content').css('left', '8px');
			}
		}
	},
	// layer 弹出层
    openFrame : function(title, url, width, height) { //layer frame窗口
        this.layerWindow = layer.open({
            type: 2,
            anim: -1,
            resize: false,
            title: title,
            shadeClose: false,
            maxmin: true,
            shade: 0.6,
            area: [width || '90%', height || '90%'],
            content: url //iframe的url
        });
    },
    // 对象判空并初始化
	ifEmpty : function(obj, defaultValue) {
		return this.isEmpty(obj) ? this.isEmpty(defaultValue) ? '' : defaultValue : obj;
	},
	// 对象判空
	isEmpty : function(arg) {
		if (arg == undefined || arg == null) {
			return true;
		}
		if (typeof arg == 'string') {
			return arg == '' ? true : false;
		} else if (typeof arg == 'object') {
			if (arg instanceof Array && arg.length == 0) {
				return true;
			} else {
				return false;
			}
		} else if (typeof arg == 'number') {
			return false;
		} else{
			return !!arg;
		}
	},
	// 对象不为空
	isNotEmpty: function(arg) {
		return !this.isEmpty(arg);
	},
	// 计算文件大小
	getFileSize : function(size){ //根据大小计算文件大小
		var unit = ["B", "KB", "MB", "GB", "TB"];
		var result = size,i = 0;
		for (; i <= 4; i++) {
			if (result > 1024) {
				result = result / 1024;
				result = result.toFixed(2);
			} else {
				break;
			}
		}
		if (i > 4) { i= 4;}
		return result + unit[i];
	},
	// 格式化时间
	formatDate : function (date, fmt) { //author: meizz 
	    var o = {
	        "M+": date.getMonth() + 1, //月份 
	        "d+": date.getDate(), //日 
	        "h+": date.getHours(), //小时 
	        "m+": date.getMinutes(), //分 
	        "s+": date.getSeconds(), //秒 
	        "q+": Math.floor((date.getMonth() + 3) / 3), //季度 
	        "S": date.getMilliseconds() //毫秒 
	    };
	    if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (date.getFullYear() + "").substr(4 - RegExp.$1.length));
	    for (var k in o)
	    if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
	    return fmt;
	},
	// ajax 封装
	ajax : function(url, param, success, method, async) { //异步请求
		var _this = this;
		if (async == undefined) {
			async = true;
		}
		$.ajax({
			url : url,
			data : param,
			type : method || "get",
			async : async,
			success : function(data) {
				if (data.status == 1) {
					success && success(data.data);
					return ;
				}
				if (data.message) {
					_this.tipBox(data.message);
				} else {
					_this.tipBox(i18n['systemErr']);
				}
			},
			error : function(data) {
				_this.tipBox(i18n['systemErr']);
			}
		});
	},
	// 对象深拷贝
	clone : function(data){ 
		if (data == null) return null;
		var newData = null, _this = this;
		if (typeof data == 'object') {
			if (data.length != undefined) {
				newData = [];
				$.each(data, function(i, value) {
					newData.push(_this.clone(value));
				});
			} else {
				newData = {};
				$.each(data, function(key, value) {
					newData[key] = _this.clone(value);
				});
			}
		} else {
			newData = data;
		}
		return newData;
	},
	// map转list
	mapTranList: function(mapTemp) {
		var listTemp = [];
		for(var key in mapTemp){
			listTemp.push(mapTemp[key]);
		}
		return listTemp;
	},
	// jq 文件上传封装, verificationImageFileMap: 文件校验map, 重复文件不再上传
	uploadFile: function(event, uploadFileUrl, verificationImageFileMap) {
		var response = {
			'successFiles' : [],
			'errorFiles': []
		};
		var tempFileName = '';
		for (var i = 0; i < event.target.files.length; i++) {
			var formData = new FormData();
			var file = event.target.files[i];
			tempFileName = file.name;
			var fileKey = tempFileName + file.size;
			if (this.isEmpty(verificationImageFileMap) || this.isEmpty(verificationImageFileMap[fileKey])) {
				formData.append('file', file);
				formData.append("name", tempFileName);
				$.ajax({
					url : uploadFileUrl,
					type : 'POST',
					data : formData,
					// 告诉jQuery不要去处理发送的数据
					processData : false,
					// 告诉jQuery不要去设置Content-Type请求头
					contentType : false,
					async: false,
					beforeSend:function(){
					},
					success : function(responseStr) {
						response.successFiles.push(responseStr);
					},
					error : function(responseStr) {
						console.log("文件上传失败!" + responseStr);
						response.errorFiles.push({
							'fileName': tempFileName
						});
					}
				});
			}
		}
		e.target.value='';
		return response;
	},

	/* layui 等待 */
    showLoad: function (msg) {
        return layer.msg(msg, {
            icon: 16,
            shade: [0.5, '#f5f5f5'],
            scrollbar: false,
            offset: 'auto',
            time: 100000
        });
    },
    closeLoad: function (index) {
        layer.close(index);
    },
    showSuccess: function (msg) {
        layer.msg(msg, {time: 1500, offset: 'auto', icon: 1});
    },
    showFail: function (msg) {
        layer.msg(msg, {time: 1500, offset: 'auto', icon: 2});
    },
    closeLayer : function() {
        if (!!this.layerWindow) {
            layer.close(this.layerWindow);
        } else {
			var index= window.parent.layer.getFrameIndex(window.name)
			window.parent.layer.close(index);
		}
    },
    // 页面间传递数据 parent.postMessage(data);
	onMessage : function(callback) { //监听发送到本窗口的消息
		if (window.addEventListener){
			window.addEventListener('message',function(event){
				callback && callback(event);
			}, false);
		} else if(window.attachEvent) {
			window.attachEvent("onmessage", function (event) {
				callback && callback(event);
			});
		}
	},
	// 字符串处理
	filterString: function(content, type) {
    	var _this = this;
    	// 过滤中文
    	if (type === 1) {
			return content.replace(/[\u4e00-\u9fa5]/g,'')
		} else if (type === 2) { // 数字转中文
    		if (isNaN(Number(node.points))) {
    			return;
			}
			return _this.numberToChinese(content);
		}
	},
	// 数字转中文
    numberToChinese : function(number) {
        if (isNaN(Number(number))) {
			console.error("this is not number!");
			return number;
		}
        var chnNumChar = ["零","一","二","三","四","五","六","七","八","九"];
        var chnUnitChar = ["","十","百","千","万","亿","万亿","亿亿"];
        var strIns = '', chnStr = '';
        var unitPos = 0;
        var zero = true;
        while(number > 0){
            var v = number % 10;
            if(v === 0){
                if(!zero){
                    zero = true;
                    chnStr = chnNumChar[v] + chnStr;
                }
            }else{
                zero = false;
                strIns = chnNumChar[v];
                // 10-19不要前缀
                if (unitPos === 1 && v === 1) {
                    strIns = chnUnitChar[unitPos];
                } else {
                    strIns += chnUnitChar[unitPos]
                }
                chnStr = strIns + chnStr;
            }
            unitPos++;
            number = Math.floor(number / 10);
        }
    	return chnStr;
	},
	// 修改编辑字符后光标移动至末尾
	moveCursorEnd : function(obj) {
		obj.focus(); //解决ff不获取焦点无法定位问题
		var range = window.getSelection();//创建range
		range.selectAllChildren(obj);//range 选择obj下所有子内容
		range.collapseToEnd();//光标移至最后
	}
};

// web localStorage 存储工具类
var StorageOperation = CommUtils.StorageOp = {
	get : function (key) {
		if (window.localStorage) {
			var prefix = this.getPrefix();
			var item = window.localStorage.getItem(prefix + key);
			if (item == undefined || item == undefined || item.trim() == '') {
				return null;
			}
			item = JSON.parse(item);
			if (item.end < new Date().getMilliseconds()) {
				window.localStorage.removeItem(prefix + key);
				return null;
			}
			return item.data;
		}
	},
	set : function(key, value, timeoutMills) {
		if (CommUtils.isEmpty(timeoutMills)) {
			timeoutMills = 604800000;
		}
		if (window.localStorage) {
			var prefix = this.getPrefix();
			var data = {
				'end' : new Date().getMilliseconds() + timeoutMills,
				'data' : value
			}
			window.localStorage.setItem(prefix + key, JSON.stringify(data));
		}
	},
	getPrefix : function() {
		return 'timeout_storage_';
	}
}
```