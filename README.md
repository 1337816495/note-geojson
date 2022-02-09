# GeoJSON 规范

https://datatracker.ietf.org/doc/html/rfc7946



# 范例

```json
{
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "geometry": {
                "type": "Point",
                "coordinates": [102.0, 0.5]
            },
            "properties":{
                "prop0": "value0"
            }
        },
        {
            "type": "Feature",
            "geometry": {
                "type": "LineString",
                "coordinates": [
                    [102.0, 0.0],
                    [103.0, 1.0],
                    [104.0, 0.0],
                    [105.0, 1.0]
                ]
            },
            "properties": {
                "prop0": "value0"
            }
        },
        {
            "type": "Feature",
            "geometry": {
                "type": "Polygon",
                "coordinates": [
                    [
                        [100.0, 0.0],
                        [101.0, 0.0],
                        [101.0, 1.0],
                        [100.0, 1.0],
                        [100.0, 0.0]
                    ]
                ]
            },
            "properties": {
                "prop0": "value0"
            }
        }
    ]
}
```



# LineString

如果 `type` 为 `LineString` ，则 `coordinates` 属性是一个拥有至少 2 个 `position` 的数组（ `position` 是一个依次存储经度和纬度值的一维数组）。

```json
...
"geometry": {
	"type": "LineString",
	"coordinates": [
		[102.0, 0.0],
		[103.0, 1.0],
		[104.0, 0.0],
		[105.0, 1.0]
	]
}
...
```



# Polygon

在定义 polygon 之前，需要引入 `linear ring` 的概念。

## linear ring

-  `linear ring` 是闭合的 `LineString` ，“闭合”是指 `LineString` 的首尾 `position` 必须相同，比如一个三角形的 `linear ring` 如下：

  ```json
  ...
  "geometry": {
  	"type": "Polygon",
  	"coordinates": [
  		[
              [ x1, y1 ],
              [ x2, y2 ],
              [ x3, y3 ],
              [ x1, y1 ]
          ]
  	]
  }
  ...
  ```

  这也意味着 `linear ring` 的 `LineString` 至少拥有 4 个 `position` 。

-  `linear ring` 可以用来表示多边形的 boundary 和 hole ， boundary 的 `linear ring` 呈现逆时针方向， hole 的 `linear ring` 呈现顺时针方向。右手系的三维直角坐标系的正方向是逆时针，左手系的三维直角坐标系的正方向是顺时针， `three.js` 的三维直角坐标系是左手系。

> 注：
>
> 1.  `earcut` 的 `vertices` 参数是一个 flat array ，而不是 `linear ring` 。
> 2.  `earcut` 的 `vertices` 参数不要求首尾闭合，也不推荐提供首尾闭合的 `vertices` 参数，因为官方示例的数据是首尾不闭合的。
> 3.  `earcut` 使用 `holes` 参数来控制 boundary 和 hole 的剖分，不需要基于 `vertices` 的顺逆时针方向。

## Polygon

For type "Polygon", the "coordinates" member MUST be an array of linear ring coordinate arrays.

如果 `type` 是 `Polygon` ，则 `coordinates` 属性是一个拥有至少 1 个 `linear ring` 的数组，且第一个 `linear ring` 代表 boundary ，其余后续的 `linear ring` 都代表 hole 。