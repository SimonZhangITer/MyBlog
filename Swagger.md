> npm install

<table class="table table-bordered table-striped table-condensed">
   <tr>
      <td>Field Name</td>
      <td>Type</td>
      <td>Description</td>
   </tr>
   <tr>
      <td>title</td>
      <td>string</td>
      <td>Required. The title of the application.</td>
   </tr>
   <tr>
      <td>description</td>
      <td>string</td>
      <td>A short description of the application. GFM syntax can be used for rich text representation.</td>
   </tr>
   <tr>
      <td>termsOfService</td>
      <td>string</td>
      <td>The Terms of Service for the API.</td>
   </tr>
   <tr>
      <td>contact</td>
      <td>Contact Object</td>
      <td>The contact information for the exposed API.</td>
   </tr>
   <tr>
      <td>license</td>
      <td>License Object</td>
      <td>The license information for the exposed API.</td>
   </tr>
   <tr>
      <td>version</td>
      <td>string</td>
      <td>Required Provides the version of the application API (not to be confused with the specification version).</td>
   </tr>
</table>



tags 所属模块 [] 可以有多个模块

summary 接口名称 string

description 描述信息 string

schemes 请求协议 http/https []

consumes 请求格式 application/json []

produces 响应格式 application/json []

parameters 请求参数 [
{

	name 参数名
	
	in 参数位置 formData
	
	description 参数描述
	
	required 是否必填
	
	type 参数类型 string/integer等
	
	defalut 默认值
	
	format 
}]

responses 响应信息 后台设置的返回编码以及返回信息
