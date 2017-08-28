[English](README.md)

<h1 id="id_top">YAS (Yet Another Schema)</h1>

Copyright (c) 2016

* [定义](#id_definition)
* [特性](#id_feature)
* [基础规范](#id_standard)
    * [格式](#id_format)
    * [数据类型](#id_types)
    * [数组](#id_array)
    * [字典](#id_dict)
    * [嵌套对象](#id_nested_object)
    * [标签](#id_tags)
* [扩展规范](#id_standard_ex)
    * [多层嵌套对象](#id_nested_objects)
    * [扩展标签](#id_tags_ex)
* [范例](#id_samples)
    * [跨协议范例](#id_samples_standard)
    * [文本协议范例](#id_samples_txt)
    * [二进制协议范例](#id_samples_bin)
    * [多层嵌套对象范例](#id_samples_nest)
* [附录](#id_appendix)
    * [FAQ](#id_faq) 
    * [License](#id_license)
    * [作者](#id_authors)
    * [相关实现](#id_implements)

<h2 id="id_definition">定义</h2>

YAS (Yet Another Schema) 是一套用于描述数据格式的通用规范。类似于 `protobuf` 的 `.proto` 文件。

[回顶部](#id_top)

<h2 id="id_feature">特性</h2>

* **极简**  
描述语法的标签极少，只需要 **7个** 即可完整描述数据格式。
* **跨协议**  
支持 `json`, `xml`, `binary`。
* **灵活**  
参考[扩展规范](#id_standard_ex)。

[回顶部](#id_top)

<h2 id="id_standard">基础规范</h2>

<h3 id="id_format">格式</h3>

YAS 使用 `json` 作为描述格式。

[回顶部](#id_top)

<h3 id="id_types">数据类型</h3>

YAS 定义的基本数据类型包括如下：

* `bool`
* `int8_t`
* `uint8_t`
* `int16_t`
* `uint16_t`
* `int32_t`
* `uint32_t`
* `int64_t`
* `uint64_t`
* `float`
* `double`
* `string`

[回顶部](#id_top)

<h3 id="id_array">数组</h3>

描述数组的语法为：

    [type-str]
其中 `type-str` 可以为任意类型。

[回顶部](#id_top)

<h3 id="id_dict">字典</h3>

描述字典的语法为：

    {type-str}
其中 `type-str` 可以为任意类型。

[回顶部](#id_top)

<h3 id="id_nested_object">嵌套对象</h3>

[样例](#id_samples_nest)

* 各个 **不同类型** 的对象之间可以嵌套，例如 `object_t` 里面可以嵌套定义 `object_base_t` 的对象。
* **被嵌套的对象必须定义在前面** 。例如， `object_base_t` 必须定义在 `object_t` 的前面。
* 不能定义 **自我嵌套** 的类型。

[回顶部](#id_top)

<h3 id="id_tags">标签</h3>

以下是 `YAS` 所支持的标签的结构：

[structs](#id_tag_structs)  
　　[`struct`](#id_tag_struct)  
　　　　[type](#id_tag_type)  
　　　　[members](#id_tag_members)  
　　　　　　[`member`](#id_tag_member)  
　　　　　　　　[`field_type`](#id_tag_field_type)  
　　　　　　　　[`field_name`](#id_tag_field_name)  

备注：由于 [structs](#id_tag_structs) 是数组，因此需要 [`struct`](#id_tag_struct) 作为 **过渡标签** 来描述数组成员。这样的标签并不会作为单独的 `key` 出现在描述文件中。

<h4 id="id_tag_structs">structs</h4>

**类型:** `array`

**值:** `[<struct>, <struct> ... <struct>]`

**属性:** 必须

**父节点:** 无

数据类型列表，每一个子项都是 [struct](#id_tag_struct)

[回顶部](#id_top)

<h4 id="id_tag_struct">struct</h4>

**类型:** `object`

**值:** `{ "name": <name-str>, "members": <members> }`

**属性:** 必须

**父节点:** [structs](#id_tag_structs)

描述单个对象结构，包含如下成员：

* [type](#id_tag_type)
* [members](#id_tag_members)

[回顶部](#id_top)

<h4 id="id_tag_type">type</h4>

**类型:** `string`

**值:** `<type-str>`

**属性:** 必须

**父节点:** [struct](#id_tag_struct)

用于描述数据的类型， **任意两个对象的类型不可重复** 。

推荐的命名风格：

* 字母全小写
* 单词之间以下划线分割
* 以 `_t` 作为类型结尾

[回顶部](#id_top)

<h4 id="id_tag_members">members</h4>

**类型:** `array`

**值:** `[<member>, <member> ... <member>]`

**属性:** 必须

**父节点:** [struct](#id_tag_struct)

对象成员列表，每一个子项都是 [member](#id_tag_member)

[回顶部](#id_top)

<h4 id="id_tag_member">member</h4>

**类型:** `array`

**值:** `[<field_type>, <field_name>]`

**属性:** 必须

**父节点:** [members](#id_tag_members)

对象字段的定义，包含如下成员：

* [`field_type`](#id_tag_field_type)
* [`field_name`](#id_tag_field_name)

[回顶部](#id_top)

<h4 id="id_tag_field_type">field_type</h4>

**类型:** `string`

**值:** `<type-str>`

**属性:** 必须

**父节点:** [member](#id_tag_member)

用于描述字段的类型。

[回顶部](#id_top)

<h4 id="id_tag_field_name">field_name</h4>

**类型:** `string`

**值:** `<name-str>`

**属性:** 必须

**父节点:** [member](#id_tag_member)

用于描述字段的名称， **在同一个对象中不可重名** 。

[回顶部](#id_top)

<h2 id="id_standard_ex">扩展规范</h2>

<h3 id="id_nested_objects">多层嵌套对象</h3>

* **数组和字典之间可以相互嵌套，且嵌套层级不限**。

例如：

    [{[object_base_t]}]
该类型在C++中被展开后生成的类型如下：

    std::vector < std::map < std::string, std::vector <object_base_t> > >

[回顶部](#id_top)

<h3 id="id_tags_ex">扩展标签</h3>

文本协议：

[`member`](#id_tag_txt_member)  
　　[`field_type`](#id_tag_field_type)  
　　[`field_name`](#id_tag_field_name)  
　　[`default_value`](#id_tag_default_value) 

二进制协议：

[`member`](#id_tag_bin_member)  
　　[`field_type`](#id_tag_field_type)  
　　[`field_name`](#id_tag_field_name)  
　　[`field_id`](#id_tag_field_id) 

<h4 id="id_tag_txt_member">member</h4>

**类型:** `array`

**值:** `[<field_type>, <field_name>, <default_value>]`

**属性:** 必须

**父节点:** [members](#id_tag_members)

对象字段的定义，包含如下成员：

* [`field_type`](#id_tag_field_type)
* [`field_name`](#id_tag_field_name)  
* [`default_value`](#id_tag_default_value)

[回顶部](#id_top)

<h4 id="id_tag_default_value">default_value</h4>

**类型:** `string`

**值:** `<value-str>`

**属性:** 可选

**父节点:** [member](#id_tag_txt_member)

用于描述字段的默认值，以 **字符串** 的形式来描述。

这样的定义是不合法的：

    ...
    ["bool", "bool_val", true],
    ["int8_t", "int8_val", 'a'],
    ["int32_t", "int32_val", -128]
    ...
    

应当更改为这样：

    ...
    ["bool", "bool_val", "true"],
    ["int8_t", "int8_val", "'a'"],
    ["int32_t", "int32_val", "-128"]
    ...

字符串类型的默认值可以直接这样写：

    ["string", "str_val", "test"]

[回顶部](#id_top)

<h4 id="id_tag_bin_member">member</h4>

**类型:** `array`

**值:** `[<field_type>, <field_name>, <field_id>]`

**属性:** 必须

**父节点:** [members](#id_tag_members)

对象字段的定义，包含如下成员：

* [`field_type`](#id_tag_field_type)
* [`field_name`](#id_tag_field_name)  
* [`field_id`](#id_tag_field_id)

[回顶部](#id_top)

<h4 id="id_tag_field_id">field_id</h4>

**类型:** `string`

**值:** `<id-str>`

**属性:** 可选

**父节点:** [member](#id_tag_bin_member)

用于描述字段的唯一数字 ID，以 **字符串** 的形式来描述。**在同一个对象中不同字段的ID不可重复** 。

这样的定义是不合法的：

    ...
    ["bool", "bool_val", 100],
    ["int8_t", "int8_val", 101],
    ["int32_t", "int32_val", 102]
    ...
    

应当更改为这样：

    ...
    ["bool", "bool_val", "100"],
    ["int8_t", "int8_val", "101"],
    ["int32_t", "int32_val", "102"]
    ...

[回顶部](#id_top)

<h2 id="id_samples">范例</h2>

<h3 id="id_samples_standard">跨协议范例</h3>

    {
        "structs": 
        [
            {
                "type": "perf_object_t",
                "members": 
                [
                    ["bool", "bool_val"],
                    ["int8_t", "int8_val"],
                    ["uint8_t", "uint8_val"],
                    ["int16_t", "int16_val"],
                    ["uint16_t", "uint16_val"],
                    ["int32_t", "int32_val"],
                    ["uint32_t", "uint32_val"],
                    ["int64_t", "int64_val"],
                    ["uint64_t", "uint64_val"],
                    ["float", "float_val"],
                    ["double", "double_val"],
                    ["string", "str_val"],
                    ["[int32_t]", "vec_val"],
                    ["{string}", "dict_val"]
                ]
            }
        ]
    }

备注：该格式是最通用的描述方法，即可适用于文本协议，也可适用于二进制协议。

[回顶部](#id_top)

<h3 id="id_samples_txt">文本协议范例</h3>

    {
        "structs": 
        [
            {
                "type": "sample_struct_t",
                "members": 
                [
                    ["bool", "bool_val", "true"],
                    ["string", "str_val", "test"],
                    ["int32_t", "int_val", "-111111"],
                    ["uint32_t", "uint_val", "111111"],
                    ["double", "double_val", "111111.111111"],
                    ["int8_t", "char_val", "'a'"],
                    ["uint8_t", "uchar_val", "128"],
                    ["int16_t", "short_val", "-256"],
                    ["uint16_t", "ushort_val", "512"],
                    ["int64_t", "int64_val", "-9223372036854775807"],
                    ["uint64_t", "uint64_val", "0xffffffffffffffff"],
                    ["float", "float_val", "111111.111111"],
                    ["[int32_t]", "vec_val"],
                    ["{string}", "str_map_val"]
                ]
            }
        ]
    }

备注：该描述使用了扩展规范的默认值语义。

[回顶部](#id_top)

<h3 id="id_samples_bin">二进制协议范例</h3>

    {
        "structs": [
            {
                "type": "sample_struct_t",
                "members": [
                    ["int8_t", "int8_val", "1"],
                    ["uint8_t", "uint8_val"],
                    ["string", "str_val", "10000"],
                    ["[string]", "str_arr_val"],
                    ["{string}", "str_dict_val", "2"]
                ]
            },
            {
                "type": "sample_object_t",
                "members": [
                    ["sample_struct_t", "obj"],
                    ["[sample_struct_t]", "arr"],
                    ["{sample_struct_t}", "dict"]
                ]
            }
        ]
    }

备注：该描述使用了扩展规范的字段ID语义。

[回顶部](#id_top)

<h3 id="id_samples_nest">多层嵌套对象范例</h3>

文本协议:

	{
	    "structs": 
	    [
	        {
	            "type": "object_base_t",
	            "members": 
	            [
                    ["bool", "bool_val", "true"],
	                ["int8_t", "int8_val"],
	                ["uint8_t", "uint8_val"],
	                ["int16_t", "int16_val"],
	                ["uint16_t", "uint16_val"],
	                ["int32_t", "int32_val", "-128"],
	                ["uint32_t", "uint32_val", "65536"],
	                ["int64_t", "int64_val"],
	                ["uint64_t", "uint64_val", "0xffffffffffffffff"],
	                ["float", "float_val"],
	                ["double", "double_val"],
	                ["string", "str_val", "test"],
	                ["[int32_t]", "vec_val"],
	                ["{string}", "dict_val"]
	            ]
	        },
            {
	            "type": "object_t",
	            "members": 
	            [
	                ["object_base_t", "obj_val"],
                    ["[object_base_t]", "obj_vec_val"],
                    ["{object_base_t}", "obj_dict_val"],
                    ["{[object_base_t]}", "obj_vec_dict_val"],
                    ["[{object_base_t}]", "obj_dict_vec_val"],
                    ["[[object_base_t]]", "obj_vec_vec_val"],
                    ["{{object_base_t}}", "obj_dict_dict_val"],
                    ["[{[object_base_t]}]", "obj_vec_dict_vec_val"]
	            ]
	        }
	    ]
	}

二进制协议：

    {
	    "structs": 
	    [
	        {
	            "type": "object_base_t",
	            "members": 
	            [
                    ["bool", "bool_val", "100"],
	                ["int8_t", "int8_val"],
	                ["uint8_t", "uint8_val"],
	                ["int16_t", "int16_val"],
	                ["uint16_t", "uint16_val"],
	                ["int32_t", "int32_val", "101"],
	                ["uint32_t", "uint32_val", "102"],
	                ["int64_t", "int64_val"],
	                ["uint64_t", "uint64_val", "103"],
	                ["float", "float_val"],
	                ["double", "double_val"],
	                ["string", "str_val", "110"],
	                ["[int32_t]", "vec_val"],
	                ["{string}", "dict_val"]
	            ]
	        },
            {
	            "type": "object_t",
	            "members": 
	            [
	                ["object_base_t", "obj_val"],
                    ["[object_base_t]", "obj_vec_val"],
                    ["{object_base_t}", "obj_dict_val"],
                    ["{[object_base_t]}", "obj_vec_dict_val"],
                    ["[{object_base_t}]", "obj_dict_vec_val"],
                    ["[[object_base_t]]", "obj_vec_vec_val"],
                    ["{{object_base_t}}", "obj_dict_dict_val"],
                    ["[{[object_base_t]}]", "obj_vec_dict_vec_val"]
	            ]
	        }
	    ]
	}

[回顶部](#id_top)

<h2 id="id_appendix">附录</h2>

<h3 id="id_faq">FAQ</h3>

### Q: 既然已经有了protobuf, thrift等工业级标准，为什么还弄一个YAS出来？ ###

首先，protobuf 和 thrift 等工业级标准非常强大，向巨头致敬 ^_^  
但是，正是因为是巨头推出的标准，它们各自的 schema 是 **彼此不兼容** 的，这是其一；  
其二，正是因为是巨头推出的标准，它们各自的协议是 **彼此不兼容** 的；  
其三，以目前的实现度来看，无论是 protobuf 还是 thrift，还是后来出现的 flatbuffers，它们的 schema 文件尚不能做到 **跨协议** 的描述；  
最后一点，无论是 protobuf 还是 thrift，它们的 schema 语法都已经变的 **非常的复杂**，其语法标签数量已经 **远远大于 7 个** 了。

基于以上的原因，YAS出现了。YAS的设计目标如下：

* 极简  
7个以内的标签即可基本掌握全部语法。
* 跨协议  
无论是文本协议，还是二进制协议，统一用一套schema作为描述文件。
* 灵活  
扩展规范以极简的语法来描述多层嵌套对象，这一点无论是 protobuf 还是 thrift 都不支持。

[回顶部](#id_top)

### Q: 多层嵌套对象在基础规范里如何实现？ ###

基础规范 **不支持** 多层嵌套对象的写法，例如：

    {
	    "structs": 
	    [
	        {
	            "type": "object_base_t",
	            "members": 
	            [
                    ["bool", "bool_val"]
	            ]
	        },
            {
	            "type": "object_t",
	            "members": 
	            [
                    ["{[object_base_t]}", "obj_vec_dict_val"]
	            ]
	        }
	    ]
	}

可以通过定义一个 **中间对象** 达到类似的效果：

    {
	    "structs": 
	    [
	        {
	            "type": "object_base_t",
	            "members": 
	            [
                    ["bool", "bool_val"]
	            ]
	        },
            {
	            "type": "object_base_array_t",
	            "members": 
	            [
                    ["[object_base_t]", "obj_arr_val"]
	            ]
	        },
            {
	            "type": "object_t",
	            "members": 
	            [
                    ["{object_base_array_t}", "obj_vec_dict_val"]
	            ]
	        }
	    ]
	}

[回顶部](#id_top)

### Q: 为什么 `dict` 只支持以字符串作为键？考虑过支持整型吗？ ###

第一，YAS的设计原则是 **极简** ；  
第二，**大部分情况下**，字符串作为键足够了。增加其他的类型会把设计搞复杂。  

判断一个设计是否是好的设计，不在于是否还能增加多少功能， **而在于现有的功能是否减无可减**。

[回顶部](#id_top)

### Q: [扩展标签](#id_tags_ex)中文本协议和二进制协议为什么使用了不同的标签？ ###
  
对于文本协议，字段的名称具备唯一性，因此不需要一个单独的 `field_id` 标签； `default_value` 很常用，尤其是用于配置文件的时候。  
对于二进制协议，为了保证编解码的性能以及数据的压缩性，将字段名作为唯一标识符号是不合适的，因此需要一个单独的 `field_id` 字段。  

[回顶部](#id_top)

<h3 id="id_license">LICENSE</h3>

YAS is licensed under [New BSD License](https://opensource.org/licenses/BSD-3-Clause), a very flexible license to use.

[回顶部](#id_top)

<h3 id="id_authors">作者</h3>
  
* ChengZhuo（jobs，yao050421103@163.com)  

[回顶部](#id_top)

<h3 id="id_implements">相关实现</h3>

* [slothjson](https://github.com/jobs-github/slothjson)
* [slothxml](https://github.com/jobs-github/slothxml)
* [rawbuf](https://github.com/jobs-github/rawbuf)

[回顶部](#id_top)