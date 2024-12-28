# Enhancing the Detail Page (cải thiện detail Page)

Khi router triển khai,model của detail page sẽ được update cho từng item. Cải tiến detail page để hiện thị thông tin cụ thể cho sản phẩm đã chọn

```xml
<mvc:View
	controllerName="sap.ui.demo.fiori2.controller.Detail"
	xmlns="sap.uxap"
	xmlns:m="sap.m"
	xmlns:f="sap.f"
	xmlns:form="sap.ui.layout.form"
	xmlns:mvc="sap.ui.core.mvc">
	<ObjectPageLayout
		id="ObjectPageLayout"
		showTitleInHeaderContent="true"
		alwaysShowContentHeader="false"
		preserveHeaderStateOnScroll="false"
		headerContentPinnable="true"
		isChildPage="true"
		upperCaseAnchorBar="false">
		<headerTitle>
			<ObjectPageDynamicHeaderTitle>
				<expandedHeading>
					<m:Title text="{products>Name}" wrapping="true" class="sapUiSmallMarginEnd"/>
				</expandedHeading>

				<snappedHeading>
					<m:FlexBox wrap="Wrap" fitContainer="true" alignItems="Center">
						<m:FlexBox wrap="NoWrap" fitContainer="true" alignItems="Center" class="sapUiTinyMarginEnd">
							<f:Avatar
								src="https://ui5.sap.com/{products>ProductPicUrl}"
								displaySize="S"
								displayShape="Square"
								class="sapUiTinyMarginEnd"/>
							<m:Title text="{products>Name}" wrapping="true"/>
						</m:FlexBox>
					</m:FlexBox>
				</snappedHeading>

				<actions>
```

Sử dụng expandedHeading(Chỉ hiện ở trạng thái mở rộng) and snappedHeading(chỉ hiện ở trạng thái khi thu gon collapsed) aggregations. Nội dung đc hiển thị tuỳ thuộc vào trạng thái của header `expanded or collapsed.`

```xml
		...
		<headerContent>
			<m:FlexBox wrap="Wrap" fitContainer="true" alignItems="Stretch">
				<f:Avatar
					src="https://ui5.sap.com/{products>ProductPicUrl}"
					displaySize="L"
					displayShape="Square"
					class="sapUiTinyMarginEnd">
				</f:Avatar>
				<m:VBox justifyContent="Center" class="sapUiSmallMarginEnd">
					<m:Label text="Main Category"/>
					<m:Text text="{products>MainCategory}"/>
				</m:VBox>
				<m:VBox justifyContent="Center" class="sapUiSmallMarginEnd">
					<m:Label text="Subcategory"/>
					<m:Text text="{products>Category}"/>
				</m:VBox>
				<m:VBox justifyContent="Center" class="sapUiSmallMarginEnd">
					<m:Label text="Price"/>
					<m:ObjectNumber number="{products>CurrencyCode} {products>Price}" emphasized="false"/>
				</m:VBox>
			</m:FlexBox>
		</headerContent>
```

Cung cấp `headerContent` các thuộc tính để hình ảnh, Main Category, Category and Price. được cung cấp từ products.json

```xml
<form:content>
									<m:Label text="Product ID"/>
									<m:Text text="{products>ProductId}"/>
									<m:Label text="Description"/>
									<m:Text text="{products>Description}"/>
									<m:Label text="Supplier"/>
									<m:Text text="{products>SupplierName}"/>
								</form:content>
```

_General Information_ hiển thị `Product ID`, `Description` and `Supplie` cho từng product
