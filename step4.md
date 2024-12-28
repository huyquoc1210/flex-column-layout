# Adding a Detail Page

Thêm trang chi tiết trống

```xml
<mvc:View
	xmlns:mvc="sap.ui.core.mvc">
</mvc:View>
```

Tạo trang chi tiết `Detail.view.xml` trống

```xml
<FlexibleColumnLayout id="flexibleColumnLayout" backgroundDesign="Solid">
		<beginColumnPages>
			<mvc:XMLView id="beginView" viewName="sap.ui.demo.fiori2.view.List"/>
		</beginColumnPages>
		<midColumnPages>
			<mvc:XMLView id="detailView" viewName="sap.ui.demo.fiori2.view.Detail"/>
		</midColumnPages>
	</FlexibleColumnLayout>
```

Thêm detail page trong `FlexibleColumnLayout's` và sử dụng `midColumnPages` aggregation trong file App.view.xml.Detail page sẽ hiển thị ở middle column

```xml title='List.view.xml'
<ColumnListItem type="Navigation" press=".onListItemPress">
```

Thêm press handler cho mỗi item `ColumnListItem in the List.view.xml.`

```js
sap.ui.define([
	"sap/ui/model/json/JSONModel",
	"sap/ui/core/mvc/Controller",
	"sap/ui/model/Filter",
	"sap/ui/model/FilterOperator",
	'sap/ui/model/Sorter',
	'sap/m/MessageBox',
	'sap/f/library'
], function (JSONModel, Controller, Filter, FilterOperator, Sorter, MessageBox, fioriLibrary) {
	"use strict";
		...

		...
		onSort: function () {
			this._bDescendingSort = !this._bDescendingSort;
			var oBinding = this.oProductsTable.getBinding("items"),
				oSorter = new Sorter("Name", this._bDescendingSort);

			oBinding.sort(oSorter);
		},

		onListItemPress: function () {
			var oFCL = this.oView.getParent().getParent();

			oFCL.setLayout(fioriLibrary.LayoutType.TwoColumnsMidExpanded);
		}
	});
});
```

Trong `List.controller.js`. thêm function `onListItemPress` đển press handler, bố cục layout thay đổi do `TwoColumnBeginExpanded`.

- TwoColumnBeginExpanded: sẽ có 2 cột, cột đầu tiên lớn hon cột thứ hai,
- TwoColumnsMidExpanded: sẽ có 2 cột, cột thứ 2 lớn hơn cột đầu tiên

Tham khảo ở đây https://sapui5.hana.ondemand.com/#/topic/3b9f760da5b64adf8db7f95247879086
Muốn lấy được nó
https://sapui5.hana.ondemand.com/#/api/sap.f.LayoutType%23overview
