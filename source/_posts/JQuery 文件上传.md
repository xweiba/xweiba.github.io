---
title: JQuery 文件上传
date: 2018-05-23 13:02:42
tags:
 - JQuery
categories:
 - JQuery
---

# Java
后端接收文件:
```
@RequestMapping(value = "/uploaded")
@ResponseBody
public String uploaded(MultipartFile file){
    return file.getOriginalFilename();
}
```

# posoman 测试
注意不要请求头
![](https://ws1.sinaimg.cn/large/b08fb805gy1g5r90msxddj210g09tdg5.jpg)

# 可能会遇到的错误
Current request is not a multipart request 错误: 去掉请求头

# web 上传
## html
thymeleaf + Vue 示范
```html
<input class="file" type="file" value="上传" id="file" multiple="multiple" accept="image/png,image/jpeg" v-on:input="uploadConfig"/>
```
## js
```js
// 上传业务方法
uploadConfig: function(e) {
    var _this = this;
    var verificationImageFiles = _this.verificationImageFiles(e);
    var responses = CommUtils.uploadFile(e, EditPaperWorkPage.uploadPaperWorkImagesUrl, verificationImageFiles);
    if (CommUtils.isNotEmpty(responses) && CommUtils.isNotEmpty(responses.successFiles)) {
        // 添加排序字段
        $.each(responses.successFiles, function (index, response) {
            $.extend(response, {
                'sequencing': index
            });
            if (CommUtils.isEmpty(_this.paper.paperWorkFileList )) {
                _this.paper.paperWorkFileList = [];
            }
            _this.paper.paperWorkFileList.push(response);
            _this.paper.isSaved = false;
        });
        if (!this.isShowImages) {
            _this.changeImageShow();
        }
        CommUtils.showSuccess(i18n['file_upload_success']);
    }
    
    if (CommUtils.isNotEmpty(responses) && CommUtils.isNotEmpty(responses.errorFiles)) {
        var errorFileNames = '';
        $.each(responses.errorFiles, function (index, errorFile) {
            if (CommUtils.isEmpty(errorFileNames)) {
                errorFileNames += errorFile.fileName;
            } else {
                errorFileNames += "," + errorFile.fileName;
            }
        });
        if (CommUtils.isNotEmpty(errorFileNames)) {
            CommUtils.showFail(errorFileNames + i18n['file_upload_error']);
        }
    }
},
// 过滤重复文件
verificationImageFiles: function(e) {
    var _this = this;

    var uploadImagesMap = {};
    var verificationImageFileMap = {};
    var duplicateFileNames = "";
    $.each(_this.paper.paperWorkFileList, function (index, file) {
        var fileKey = file.fileName + file.size;
        uploadImagesMap[fileKey] = file;
    });
    $.each(e.target.files, function (index, file) {
        var fileKey = file.name + file.size;
        if (uploadImagesMap[fileKey] || file.type.indexOf("image") < 0) {
            verificationImageFileMap[fileKey] = file;
            if (CommUtils.isNotEmpty(duplicateFileNames)) {
                duplicateFileNames += (',' +  file.name);
            } else {
                duplicateFileNames += file.name;
            }
        }
    });

    if (CommUtils.isNotEmpty(duplicateFileNames)) {
        CommUtils.showFail(i18n['duplicate_file'] + duplicateFileNames);
    }

    return verificationImageFileMap;
},
// 上传工具类
uploadFile: function(e, uploadFileUrl, verificationImageFileMap) {
	var response = {
		'successFiles' : [],
		'errorFiles': []
	};
	var tempFileName = '';
	for (var i = 0; i < e.target.files.length; i++) {
		var formData = new FormData();
		var file = e.target.files[i];
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

```