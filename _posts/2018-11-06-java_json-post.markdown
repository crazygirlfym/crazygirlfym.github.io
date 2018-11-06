---
layout: post
title: Java中的Json包使用心得
date: 2018-11-06 20:32:24.000000000 +09:00
tags: java, jar, 工具 
---


>首先要说下之前用的是json lib那个包，在应用过程中有个bug, 于是选用了轻量级的解析工具包org.json.jar


在用json lib这个包时，同时需要你加入其他的依赖包，需要注意各依赖包之间的版本兼容。在应用过程中，出现如下问题：

     JSONObject obj = new JSONObject();
     obj.put("test_string", "null")
     //put进去的是字符串"null"转为json对象时不能识别数据类型，结果是{"test_string", null} 。

	 obj.put("test_str", "[8]")
	 //相类似的，出现某默认数据类型时，也不能识别自动转换成了list类别。也就是说，在java中的关键字或者是类型作为字符串时转换会出现问题。
个人认为，这是个小bug。


**org.json包的简单使用**
org.json包就不会出现上述问题，能够正常识别带关键字的字符串。

 - JSONObject

这个对象和map比较像，可以往其put键值对，并且value的值允许不一样,但不允许是bean类型。

        JSONObject obj = new JSONObject();
        obj.put("test", "null");
	    ArrayList<String> l = new ArrayList<String>();
	    l.add("这");
	    l.add("是");
	    l.add("测试");
	    obj.put("list", l);

	    System.out.print(obj.toString());
	    //{"test":"null","list":["这","是","测试"]}


 导入bean类型的数据
如果将bean类型的数据，像上述方法一样导入，将有问题，存储的是一个对象，而不是值。

			JSONObject obj = new JSONObject();
			Student s = new Student(15,"Amy");
			obj.put("student", s);
			System.out.print(obj.toString());

			//{"student":"json_test.Student@4aa298b7"}

正确方法应该是

			Student s = new Student(15,"Amy");
			JSONObject obj = new JSONObject(s);
			System.out.print(obj.toString());

			//{"name":"Amy","age":15}

从文件中读取json, 即传入一个string， 把他转换为JSONObject

				JSONObject obj = new JSONObject(str);

获取其中的某个字段，不同类型采用不同方法

			String query = obj.getString("query");
			JSONArray evidences = obj.getJSONArray("passage")  //list 类型

	// evidences是个list类型，其中的元素可以继续递归获取Object对象。
	for(int i=0;i<evidences.length;i++){
		JSONObject evidence = (JSONObject)evidence.get(i);
	}

 - JSONArray
 JSONArray相对于JSONObject，它是一个有序的序列值，表现形式是包裹在方括号中，可用于JSONObject的嵌套处理中，例子如上。

在用的过程中，掌握好以上两个的使用，就能实现简单的读写json功能了。说了这么多，感觉还是python的 json读取方便。
