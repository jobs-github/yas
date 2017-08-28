[简体中文](README_ZH.md)

<h1 id="id_top">YAS (Yet Another Schema)</h1>

Copyright (c) 2016

* [Definition](#id_definition)
* [Features](#id_feature)
* [Specification](#id_standard)
    * [Format](#id_format)
    * [Types](#id_types)
    * [Array](#id_array)
    * [Dict](#id_dict)
    * [Nested Object](#id_nested_object)
    * [Tags](#id_tags)
* [Extension](#id_standard_ex)
    * [Multilayer Nested Objects](#id_nested_objects)
    * [Extended Tags](#id_tags_ex)
* [Samples](#id_samples)
    * [Cross-Protocol](#id_samples_standard)
    * [Text Protocol](#id_samples_txt)
    * [Binary Protocol](#id_samples_bin)
    * [Multilayer Nested Objects](#id_samples_nest)
* [Appendix](#id_appendix)
    * [FAQ](#id_faq) 
    * [License](#id_license)
    * [Author](#id_authors)
    * [Implements](#id_implements)

<h2 id="id_definition">Definition</h2>

YAS (Yet Another Schema) is a general specification to describe the data format, something of the kind `.proto` in `protobuf`.

[Back to top](#id_top)

<h2 id="id_feature">Features</h2>

* **Succinct**  
Few tags for grammar. Everything can be done with only **7** tags.  
* **Cross-Protocol**  
Support `json`, `xml`, `binary`.  
* **Flexible**  
Refer to [Extension](#id_standard_ex).  

[Back to top](#id_top)

<h2 id="id_standard">Specification</h2>

<h3 id="id_format">Format</h3>

YAS uses `json` as description format.  

[Back to top](#id_top)

<h3 id="id_types">Types</h3>

The built-in types of YAS include the following:

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

[Back to top](#id_top)

<h3 id="id_array">Array</h3>

You can define `array` like this:

    [type-str]
The type of `type-str` is unlimited.

[Back to top](#id_top)

<h3 id="id_dict">Dict</h3>

You can define `dict` like this:

    {type-str}
The type of `type-str` is unlimited.

[Back to top](#id_top)

<h3 id="id_nested_object">Nested Object</h3>

[Sample](#id_samples_nest)

* **Different** types of objects could have nested structure, for example, you can define `object_base_t` inside of `object_t` .
* **Nested object is supposed to be defined first**. For instance, `object_base_t` should be defined before `object_t`.
* **Self-nested** object is NOT allowed.

[Back to top](#id_top)

<h3 id="id_tags">Tags</h3>

The following is the tags' structure supported by YAS:  

[structs](#id_tag_structs)  
　　[`struct`](#id_tag_struct)  
　　　　[type](#id_tag_type)  
　　　　[members](#id_tag_members)  
　　　　　　[`member`](#id_tag_member)  
　　　　　　　　[`field_type`](#id_tag_field_type)  
　　　　　　　　[`field_name`](#id_tag_field_name)  

Comment: As [structs](#id_tag_structs) stand for array, we need [`struct`](#id_tag_struct) as **transitional** tag to describe the item of array. Such tags will NOT exist in schema as individual `key`.  

<h4 id="id_tag_structs">structs</h4>

**Type:** `array`

**Value:** `[<struct>, <struct> ... <struct>]`

**Attribute:** required

**Parent:** none

The type list of object, each of them is a [struct](#id_tag_struct).

[Back to top](#id_top)

<h4 id="id_tag_struct">struct</h4>

**Type:** `object`

**Value:** `{ "name": <name-str>, "members": <members> }`

**Attribute:** required

**Parent:** [structs](#id_tag_structs)

To describe a single data struct, including the following members:

* [type](#id_tag_type)
* [members](#id_tag_members)

[Back to top](#id_top)

<h4 id="id_tag_type">type</h4>

**Type:** `string`

**Value:** `<type-str>`

**Attribute:** required

**Parent:** [struct](#id_tag_struct)

To specify the type of object. **Duplicated types are not allowed** .

Recommended name-style:

* all lowercase
* with underscores between word
* with a trailing `_t`

[Back to top](#id_top)

<h4 id="id_tag_members">members</h4>

**Type:** `array`

**Value:** `[<member>, <member> ... <member>]`

**Attribute:** required

**Parent:** [struct](#id_tag_struct)

The member list of object, each of them is a [member](#id_tag_member)

[Back to top](#id_top)

<h4 id="id_tag_member">member</h4>

**Type:** `array`

**Value:** `[<field_type>, <field_name>]`

**Attribute:** required

**Parent:** [members](#id_tag_members)

To define a member of object, including the following items:

* [`field_type`](#id_tag_field_type)
* [`field_name`](#id_tag_field_name)

[Back to top](#id_top)

<h4 id="id_tag_field_type">field_type</h4>

**Type:** `string`

**Value:** `<type-str>`

**Attribute:** required

**Parent:** [member](#id_tag_member)

To specify the type of field.

[Back to top](#id_top)

<h4 id="id_tag_field_name">field_name</h4>

**Type:** `string`

**Value:** `<name-str>`

**Attribute:** required

**Parent:** [member](#id_tag_member)

To specify the name of field. **Duplicated names are not allowed in the same object** .

[Back to top](#id_top)

<h2 id="id_standard_ex">Extension</h2>

<h3 id="id_nested_objects">Multilayer Nested Objects</h3>

* **`array` and `dict` could be nested with each other. The nested level is unlimited** .

For example: 

    [{[object_base_t]}]
It will be parsed as the following type in C++:

    std::vector < std::map < std::string, std::vector <object_base_t> > >

[Back to top](#id_top)

<h3 id="id_tags_ex">Extended Tags</h3>

Text protocol:  

[`member`](#id_tag_txt_member)  
　　[`field_type`](#id_tag_field_type)  
　　[`field_name`](#id_tag_field_name)  
　　[`default_value`](#id_tag_default_value) 

Binary protocol：

[`member`](#id_tag_bin_member)  
　　[`field_type`](#id_tag_field_type)  
　　[`field_name`](#id_tag_field_name)  
　　[`field_id`](#id_tag_field_id) 

<h4 id="id_tag_txt_member">member</h4>

**Type:** `array`

**Value:** `[<field_type>, <field_name>, <default_value>]`

**Attribute:** required

**Parent:** [members](#id_tag_members)

To define a member of object, including the following items:

* [`field_type`](#id_tag_field_type)
* [`field_name`](#id_tag_field_name)  
* [`default_value`](#id_tag_default_value)

[Back to top](#id_top)

<h4 id="id_tag_default_value">default_value</h4>

**Type:** `string`

**Value:** `<value-str>`

**Attribute:** optional

**Parent:** [member](#id_tag_txt_member)

To specify the default value for field. It should be **string**.

It's incorrect to write like this:

    ...
    ["bool", "bool_val", true],
    ["int8_t", "int8_val", 'a'],
    ["int32_t", "int32_val", -128]
    ...
    

The right way:

    ...
    ["bool", "bool_val", "true"],
    ["int8_t", "int8_val", "'a'"],
    ["int32_t", "int32_val", "-128"]
    ...

`string` type could be defined like this:

    ["string", "str_val", "test"]

[Back to top](#id_top)

<h4 id="id_tag_bin_member">member</h4>

**Type:** `array`

**Value:** `[<field_type>, <field_name>, <field_id>]`

**Attribute:** required

**Parent:** [members](#id_tag_members)

To define a member of object, including the following items:

* [`field_type`](#id_tag_field_type)
* [`field_name`](#id_tag_field_name)  
* [`field_id`](#id_tag_field_id)

[Back to top](#id_top)

<h4 id="id_tag_field_id">field_id</h4>

**Type:** `string`

**Value:** `<id-str>`

**Attribute:** optional

**Parent:** [member](#id_tag_bin_member)

To specify the unique digital ID for field. It should be **string**. **Duplicated IDs are not allowed in the same object** .

It's incorrect to write like this:

    ...
    ["bool", "bool_val", 100],
    ["int8_t", "int8_val", 101],
    ["int32_t", "int32_val", 102]
    ...
    

The right way:

    ...
    ["bool", "bool_val", "100"],
    ["int8_t", "int8_val", "101"],
    ["int32_t", "int32_val", "102"]
    ...

[Back to top](#id_top)

<h2 id="id_samples">Samples</h2>

<h3 id="id_samples_standard">Cross-Protocol</h3>

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

Comment: this is the most generic way, which works well both in text protocol and binary protocol.

[Back to top](#id_top)

<h3 id="id_samples_txt">Text Protocol</h3>

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

Comment: this example uses the `default value` tag in extended specification.

[Back to top](#id_top)

<h3 id="id_samples_bin">Binary Protocol</h3>

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

Comment: this example uses the `field id` tag in extended specification.

[Back to top](#id_top)

<h3 id="id_samples_nest">Multilayer Nested Objects</h3>

Text protocol:  

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

Binary protocol:  

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

[Back to top](#id_top)

<h2 id="id_appendix">Appendix</h2>

<h3 id="id_faq">FAQ</h3>

### Q: What's the reason to use YAS, not the schema of protobuf, thrift and so on ? ###

We need to do honor to the industrial standards such as protobuf, thrift as they are extremely powerful. ^_^  
However, we need to bear the following disadvantages:  
Firstly, the **incompatible** schemas, as they have different "farthers";  
Secondly, the **incompatible** protocols, as they have different "farthers";  
Thirdly, their schemas are not Cross-Protocol (including protobuf, thrift, flatbuffers);  
Finally, the complicated grammar with **far more than 7** tags, either protobuf, or thrift.  

Based on the reasons above, YAS comes out. It is designed according to the following aims:  

* Succinct  
7 tags at most to describe the grammar.  
* Cross-Protocol  
One schema, multiple protocols.  
* Flexible  
By [Extension](#id_standard_ex), it's easy to define the Multilayer Nested Objects, which is not supported by either protobuf or thrift.  

[Back to top](#id_top)

### Q: How to define Multilayer Nested Objects in [Specification](#id_standard), not [Extension](#id_standard_ex)? ###

It's **not allowd** to write like this:   

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

Here is a workround with **intermediate object** :  

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

[Back to top](#id_top)

### Q: Why `dict` only supports string as key? Any plan to support integer? ###

For one thing, the key words of YAS is nothing but **succinct** ;  
for another thing, string as key works **in most cases** . Support other types will complicate design.  

Good design is not achieved when there is nothing left to add, **but when there is nothing left to take away**.

[Back to top](#id_top)

### Q: Why text protocol and binary protocol use different tags in chapter [Extended Tags](#id_tags_ex)? ###

For text protocol, field name is unique, so there is no need for tag like `field_id`. But `default_value` is frequently used, especially in configuration.  
For binary protocol, considering the data compression and the performance of encoding and decoding, it's improper to use field name as unique identifier. So we need tag like `field_id`.  

[Back to top](#id_top)

<h3 id="id_license">LICENSE</h3>

YAS is licensed under [New BSD License](https://opensource.org/licenses/BSD-3-Clause), a very flexible license to use.

[Back to top](#id_top)

<h3 id="id_authors">Author</h3>
  
* ChengZhuo（jobs，yao050421103@163.com)  

[Back to top](#id_top)

<h3 id="id_implements">Implements</h3>

* [slothjson](https://github.com/jobs-github/slothjson)
* [slothxml](https://github.com/jobs-github/slothxml)
* [rawbuf](https://github.com/jobs-github/rawbuf)

[Back to top](#id_top)