# 2024-08-05
## 图片组件（Image）
``` dart 
// 网路
Image.network(
	url,
	alignment: Alignment.bottomRight,
	color: Colors.blue,
	colorBlendMode: BlendMode.color,
	fit: BoxFit.contain,
	repeat: ImageRepeat.repeat,
)

// 本地
// pubspec.yaml assets 配置 - images/banner.png
Image.asset('images/banner.png')

// 圆图 方法1
ClipOval(
    child: Image.network(
		url,
		width: 300,
		height: 300,
		fit: BoxFit.cover,
	),
)

// 圆图 方法2
Container(
	width: 300.0, // 设置宽度
	height: 300.0, // 设置高度
	decoration: BoxDecoration(
	  color: Colors.yellow, // 设置Container的背景颜色
	  border: Border.all(
		// 设置边框的颜色和宽度
		color: Colors.blue,
		width: 2,
	  ),
	  borderRadius: BorderRadius.circular(150),
	  image: DecorationImage(
		image: NetworkImage(url),
		fit: BoxFit.cover,
	),
),
```

## 列表
``` dart
ListView(
	children: <Widget>[
		ListTile(
			leading: Image.network(
				url,
				width: 80,
				height: 80,
			),
			title: Text('疾控中心'),
			subtitle: Text('普通人口罩不必一次一换'),
		),
	],
);

// 下拉刷新，上拉加载更多
import 'dart:async';
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  const MyApp({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('container标题'),
        ),
        body: HomeContent(),
      ),
    );
  }
}

class HomeContent extends StatefulWidget {
  HomeContent({Key key}) : super(key: key);

  @override
  _HomeContentState createState() => _HomeContentState();
}

class _HomeContentState extends State<HomeContent> {
  List list = new List();
  ScrollController _controller = new ScrollController();

  // 下拉刷新方法,为list重新赋值
    Future<void> _refreshData() async {
    return await Future.delayed(Duration(seconds: 3), () {
      setState(() {
        list = List.generate(
          20,
          (index) {
            return '我是刷新的数据：$index';
          },
        );
      });
      print('下拉结束');
    });
  }

  // 初始化数据
  @override
  void initState() {
    super.initState();
    _controller.addListener(() {
      ///判断当前滑动位置是不是到达底部，触发加载更多回调
      // print('${_controller.position.pixels}---${_controller.position.maxScrollExtent}');
      if (_controller.position.pixels == _controller.position.maxScrollExtent) {
        setState(() {
          list.addAll(List.generate(10, (index) {
            return '上拉加载的数据：${list.length + index }';
          }));
        });
      }
    });
    setState(() {
      list = List.generate(25, (index) {
        return '原始数据：$index';
      });
    });
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      child: RefreshIndicator(
        onRefresh: () => _refreshData(),
        child: ListView.separated(
          itemBuilder: (content, index) {
            print(index);
            return Container(
              child: Text(list[index]),
            );
          },
          controller: _controller,
          separatorBuilder: (content, index) {
            return Divider(
              color: Colors.red,
            );
          },
          itemCount: list.length,
        ),
      ),
    );
  }
}
```

# 2024-08-06
网路请求
dio: ^3.0.0
轮播图
flutter_swiper: ^1.1.4
过渡动画
flutter_spinkit: ^3.1.0

查询 包
pub.dev