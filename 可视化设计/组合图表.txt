from pyecharts import options as opts
from pyecharts.charts import Bar, Line, Page
from pyecharts.faker import Faker


def bar() -> Bar:
    c = (
        Bar()
        .add_xaxis(Faker.days_attrs)
        .add_yaxis("产品销量", Faker.days_values)
        .set_global_opts(
            title_opts=opts.TitleOpts(title="柱状图"),
            datazoom_opts=[opts.DataZoomOpts()],
        )
    )
    return c


def line() -> Line:
    c = (
        Line()
        .add_xaxis(Faker.choose())
        .add_yaxis(
            "目标产品A的销量",
            Faker.values(),
            markpoint_opts=opts.MarkPointOpts(data=[opts.MarkPointItem(type_="min")]),
        )
        .add_yaxis(
            "目标产品B的销量",
            Faker.values(),
            markpoint_opts=opts.MarkPointOpts(data=[opts.MarkPointItem(type_="max")]),
        )
        .set_global_opts(title_opts=opts.TitleOpts(title="折线图"))
    )
    return c

def page_draggable_layout():
    page = Page(layout=Page.DraggablePageLayout)
    page.add(
        bar(),
        line(),
    )
    page.render("./output/组合图表.html")


if __name__ == "__main__":
    page_draggable_layout()