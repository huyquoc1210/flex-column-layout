# Routing

Sử dụng `sap.f.routing.Router`

![alt text](image-4.png)

```xml title="app"
<mvc:View
	controllerName="sap.ui.demo.fiori2.controller.App"
	displayBlock="true"
	height="100%"
	xmlns="sap.f"
	xmlns:mvc="sap.ui.core.mvc">
	<FlexibleColumnLayout
		id="flexibleColumnLayout"
		stateChange=".onStateChanged"
		backgroundDesign="Solid"
		layout="{/layout}"/>
</mvc:View>
```

remove beginColumnPages và endColumnPages aggregations, dùng property `layout` để thay đổi layout

```js title="app"
sap.ui.define(
  ["sap/ui/model/json/JSONModel", "sap/ui/core/mvc/Controller"],
  function (JSONModel, Controller) {
    "use strict";

    return Controller.extend("sap.ui.demo.fiori2.controller.App", {
      onInit: function () {
        this.oOwnerComponent = this.getOwnerComponent();
        this.oRouter = this.oOwnerComponent.getRouter();
        this.oRouter.attachRouteMatched(this.onRouteMatched, this);
      },

      onRouteMatched: function (oEvent) {
        var sRouteName = oEvent.getParameter("name"),
          oArguments = oEvent.getParameter("arguments");

        // Save the current route name
        this.currentRouteName = sRouteName;
        this.currentProduct = oArguments.product;
      },

      onStateChanged: function (oEvent) {
        var bIsNavigationArrow = oEvent.getParameter("isNavigationArrow"),
          sLayout = oEvent.getParameter("layout");

        // Replace the URL with the new layout if a navigation arrow was used
        if (bIsNavigationArrow) {
          this.oRouter.navTo(
            this.currentRouteName,
            { layout: sLayout, product: this.currentProduct },
            true
          );
        }
      },

      onExit: function () {
        this.oRouter.detachRouteMatched(this.onRouteMatched, this);
      },
    });
  }
);
```

Truy cập vào router và liên kết với `routeMatched`.

```js
sap.ui.define(
  [
    "sap/ui/model/json/JSONModel",
    "sap/ui/core/mvc/Controller",
    "sap/ui/model/Filter",
    "sap/ui/model/FilterOperator",
    "sap/ui/model/Sorter",
    "sap/m/MessageBox",
    "sap/f/library",
  ],
  function (
    JSONModel,
    Controller,
    Filter,
    FilterOperator,
    Sorter,
    MessageBox,
    fioriLibrary
  ) {
    "use strict";

    return Controller.extend("sap.ui.demo.fiori2.controller.List", {
      onInit: function () {
        this.oView = this.getView();
        this._bDescendingSort = false;
        this.oProductsTable = this.oView.byId("productsTable");
        this.oRouter = this.getOwnerComponent().getRouter();
      },

      onSearch: function (oEvent) {
        var oTableSearchState = [],
          sQuery = oEvent.getParameter("query");

        if (sQuery && sQuery.length > 0) {
          oTableSearchState = [
            new Filter("Name", FilterOperator.Contains, sQuery),
          ];
        }

        this.oProductsTable
          .getBinding("items")
          .filter(oTableSearchState, "Application");
      },

      onAdd: function () {
        MessageBox.show("This functionality is not ready yet.", {
          icon: MessageBox.Icon.INFORMATION,
          title: "Aw, Snap!",
          actions: [MessageBox.Action.OK],
        });
      },

      onSort: function () {
        this._bDescendingSort = !this._bDescendingSort;
        var oBinding = this.oProductsTable.getBinding("items"),
          oSorter = new Sorter("Name", this._bDescendingSort);

        oBinding.sort(oSorter);
      },

      onListItemPress: function (oEvent) {
        var productPath = oEvent
            .getSource()
            .getBindingContext("products")
            .getPath(),
          product = productPath.split("/").slice(-1).pop();

        this.oRouter.navTo("detail", {
          layout: fioriLibrary.LayoutType.TwoColumnsMidExpanded,
          product: product,
        });
      },
    });
  }
);
```

change the event handler, nhấn vào item từ list view để sử dụng router định tuyến thay vì thủ công `FlexibleColumnLayout`. gọi navTo(), định tuyến đến đấy.

```js
sap.ui.define(["sap/ui/core/mvc/Controller"], function (Controller) {
  "use strict";

  return Controller.extend("sap.ui.demo.fiori2.controller.Detail", {
    onInit: function () {
      var oOwnerComponent = this.getOwnerComponent();

      this.oRouter = oOwnerComponent.getRouter();
      this.oModel = oOwnerComponent.getModel();

      this.oRouter
        .getRoute("list")
        .attachPatternMatched(this._onProductMatched, this);
      this.oRouter
        .getRoute("detail")
        .attachPatternMatched(this._onProductMatched, this);
    },

    _onProductMatched: function (oEvent) {
      this._product =
        oEvent.getParameter("arguments").product || this._product || "0";
      this.getView().bindElement({
        path: "/ProductCollection/" + this._product,
        model: "products",
      });
    },

    onEditToggleButtonPress: function () {
      var oObjectPage = this.getView().byId("ObjectPageLayout"),
        bCurrentShowFooterState = oObjectPage.getShowFooter();

      oObjectPage.setShowFooter(!bCurrentShowFooterState);
    },

    onExit: function () {
      this.oRouter
        .getRoute("list")
        .detachPatternMatched(this._onProductMatched, this);
      this.oRouter
        .getRoute("detail")
        .detachPatternMatched(this._onProductMatched, this);
    },
  });
});
```

Liên kết table trong detail view để phẩn ánh product hiện được chọn trong list view

```js
sap.ui.define(
  ["sap/ui/core/UIComponent", "sap/ui/model/json/JSONModel", "sap/f/library"],
  function (UIComponent, JSONModel, fioriLibrary) {
    "use strict";

    return UIComponent.extend("sap.ui.demo.fiori2.Component", {
      metadata: {
        manifest: "json",
      },

      init: function () {
        var oModel, oProductsModel, oRouter;

        UIComponent.prototype.init.apply(this, arguments);

        oModel = new JSONModel();
        this.setModel(oModel);

        // set products demo model on this sample
        oProductsModel = new JSONModel(
          sap.ui.require.toUrl("sap/ui/demo/mock") + "/products.json"
        );
        oProductsModel.setSizeLimit(1000);
        this.setModel(oProductsModel, "products");

        oRouter = this.getRouter();
        oRouter.attachBeforeRouteMatched(this._onBeforeRouteMatched, this);
        oRouter.initialize();
      },

      _onBeforeRouteMatched: function (oEvent) {
        var oModel = this.getModel(),
          sLayout = oEvent.getParameters().arguments.layout;

        // If there is no layout parameter, set a default layout (normally OneColumn)
        if (!sLayout) {
          sLayout = fioriLibrary.LayoutType.OneColumn;
        }

        oModel.setProperty("/layout", sLayout);
      },
    });
  }
);
```

Khơi tạo router và liên kết onBeforeMatched event, để mô tả layout của nó

Tham khảo :
router: https://sapui5.hana.ondemand.com/#/topic/c6da1a56c2a14742b785c7bd5ca4205b
