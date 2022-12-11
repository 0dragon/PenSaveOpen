# PenSaveOpen

### 1. 실행결과

+ **초기 실행화면**
![0](https://user-images.githubusercontent.com/86341272/206889560-ca6e7de6-eacb-4a15-9164-109ae101c682.PNG)
+ **메뉴**
![2](https://user-images.githubusercontent.com/86341272/206889563-67ea5fed-5ebd-480c-8758-931aacd0507c.PNG) 
+ **저장 시 생기는 파일**

![5](https://user-images.githubusercontent.com/86341272/206889566-060d76e8-8666-4bb5-b0fc-5deb50366db3.PNG)
![6](https://user-images.githubusercontent.com/86341272/206889567-44bb38ad-1a52-438b-ad4e-77d1f1ee8c57.PNG)

#### 실행 영상
![1번 실행영상](https://user-images.githubusercontent.com/86341272/206890250-a7d8a5fa-72aa-41ea-9623-3dce32df0f7f.gif)

---
### 2. 소스코드
#### xaml 코드
```
xmlns:canvas="using:Microsoft.Graphics.Canvas.UI.Xaml"
```
```xaml
<Grid Padding="10">

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <Grid Grid.Row="0" Margin="0,0,0,10">
            <StackPanel Orientation="Horizontal">

                <ContentPresenter Background="Gainsboro" CornerRadius="8">
                    <MenuBar>
                        <MenuBarItem Title="파일">
                            <MenuFlyoutItem Text="새 파일" Click="MenuFlyoutItem_Click" />
                            <MenuFlyoutItem Text="저장" Click="MenuFlyoutItem_Click_1" />
                            <MenuFlyoutItem Text="불러오기" Click="MenuFlyoutItem_Click_2" />
                            <MenuFlyoutItem Text="종료" Click="MenuFlyoutItem_Click_3" />
                        </MenuBarItem>
                    </MenuBar>
                </ContentPresenter>

                <TextBlock Margin="8" Text="펜 굵기" VerticalAlignment="Center" />
                <ContentPresenter Margin="8" CornerRadius="8" VerticalAlignment="Center">
                    <Slider x:Name="sldPen" Width="200" Value="8" ValueChanged="sldPen_ValueChanged" />
                </ContentPresenter>
            </StackPanel>
        </Grid>

        <Grid Grid.Row="1" Grid.Column="0" CornerRadius="8" Height="500" Width="600">
            <canvas:CanvasControl
                              Draw="CanvasControl_Draw"
                              PointerEntered="CanvasControl_PointerEntered"
                              PointerPressed="CanvasControl_PointerPressed"
                              PointerReleased="CanvasControl_PointerReleased"
                              PointerMoved="CanvasControl_PointerMoved"
                              ClearColor="CornflowerBlue" />
        </Grid>

        <Grid Grid.Row="1" Grid.Column="1" CornerRadius="8" Margin="12">
            <ColorPicker Grid.Column="1" Grid.Row="1" 
                         ColorChanged="ColorPicker_ColorChanged"
                         ColorSpectrumShape="Ring"
                         IsMoreButtonVisible="False"
                         IsColorSliderVisible="True"
                         IsColorChannelTextInputVisible="True"
                         IsHexInputVisible="True"
                         IsAlphaEnabled="False"
                         IsAlphaSliderVisible="True"
                         IsAlphaTextInputVisible="True" />
        </Grid>
    </Grid>
```
#### cpp 코드
```c++
#include <microsoft.ui.xaml.window.h>
```
MainWindow() 안에 코드
```c++
auto windowNative{ this->try_as<::IWindowNative>() };
HWND hWnd{ 0 };
windowNative->get_WindowHandle(&hWnd);
SetWindowPos(hWnd, NULL, 200, 200, 970, 630, NULL);
this->Title(L"Andong National University");
```
이벤트처리기
```c++
void winrt::App1::implementation::MainWindow::CanvasControl_Draw(winrt::Microsoft::Graphics::Canvas::UI::Xaml::CanvasControl const& sender, winrt::Microsoft::Graphics::Canvas::UI::Xaml::CanvasDrawEventArgs const& args)
{
    CanvasControl canvas = sender.as<CanvasControl>();
    int n = vx.size();
    for (int i = 1; i < n; i++)
    {
        if (vx[i] == 0 && vy[i] == 0)
        {
            i++; continue;
        }
        args.DrawingSession().DrawLine(vx[i - 1], vy[i - 1], vx[i], vy[i], col[i], size[i]);
        args.DrawingSession().FillCircle(vx[i - 1], vy[i - 1], size[i] / 2, col[i]);
        args.DrawingSession().FillCircle(vx[i], vy[i], size[i] / 2, col[i]);
    }
}

void winrt::App1::implementation::MainWindow::CanvasControl_PointerMoved(winrt::Windows::Foundation::IInspectable const& sender, winrt::Microsoft::UI::Xaml::Input::PointerRoutedEventArgs const& e)
{
    CanvasControl canvas = sender.as<CanvasControl>();
    px = e.GetCurrentPoint(canvas).Position().X;
    py = e.GetCurrentPoint(canvas).Position().Y;
    if (flag)
    {
        vx.push_back(px);
        vy.push_back(py);
        size.push_back(mySize);
        col.push_back(myCol);
        canvas.Invalidate();
    }
}

void winrt::App1::implementation::MainWindow::CanvasControl_PointerPressed(winrt::Windows::Foundation::IInspectable const&, winrt::Microsoft::UI::Xaml::Input::PointerRoutedEventArgs const&)
{
    flag = true;
}

void winrt::App1::implementation::MainWindow::CanvasControl_PointerReleased(winrt::Windows::Foundation::IInspectable const&, winrt::Microsoft::UI::Xaml::Input::PointerRoutedEventArgs const&)
{
    flag = false;
    px = py = 0;
    vx.push_back(px);
    vy.push_back(py);
    col.push_back(myCol);
    size.push_back(mySize);
}

void winrt::App1::implementation::MainWindow::CanvasControl_PointerEntered(winrt::Windows::Foundation::IInspectable const& sender, winrt::Microsoft::UI::Xaml::Input::PointerRoutedEventArgs const&)
{
    CanvasControl canvas = sender.as<CanvasControl>();
    canvas.Invalidate();
}

void winrt::App1::implementation::MainWindow::ColorPicker_ColorChanged(winrt::Microsoft::UI::Xaml::Controls::ColorPicker const&, winrt::Microsoft::UI::Xaml::Controls::ColorChangedEventArgs const& args)
{
    myCol = args.NewColor();
}

void winrt::App1::implementation::MainWindow::sldPen_ValueChanged(winrt::Windows::Foundation::IInspectable const&, winrt::Microsoft::UI::Xaml::Controls::Primitives::RangeBaseValueChangedEventArgs const& e)
{
    mySize = e.NewValue();
}

void winrt::App1::implementation::MainWindow::MenuFlyoutItem_Click(winrt::Windows::Foundation::IInspectable const&, winrt::Microsoft::UI::Xaml::RoutedEventArgs const&)
{
    vx.clear();
    vy.clear();
    size.clear();
    col.clear();
}

void winrt::App1::implementation::MainWindow::MenuFlyoutItem_Click_1(winrt::Windows::Foundation::IInspectable const&, winrt::Microsoft::UI::Xaml::RoutedEventArgs const&)
{
    FILE* fp;
    fopen_s(&fp, "C:/Users/SYR/Desktop/1.txt", "w");
    
    if (fp)
    {
        int n = vx.size();
        for (int i = 1; i < n; i++)
        {
            fprintf(fp, "%d %d %d %d %d %d\n", vx[i], vy[i], size[i], col[i].R, col[i].G, col[i].B);
        }
        fclose(fp);
    }
}

void winrt::App1::implementation::MainWindow::MenuFlyoutItem_Click_2(winrt::Windows::Foundation::IInspectable const&, winrt::Microsoft::UI::Xaml::RoutedEventArgs const&)
{
    FILE* fp;
    fopen_s(&fp, "C:/Users/SYR/Desktop/1.txt", "r");

    if (fp)
    {
        vx.clear(); vy.clear(); size.clear(); col.clear();

        int x, y, s, r, g, b;
        while (EOF != fscanf_s(fp, "%d %d %d %d %d %d\n", &x, &y, &s, &r, &g, &b))
        {
            vx.push_back(x);
            vy.push_back(y);
            size.push_back(s);

            Windows::UI::Color tempCol;
            tempCol.R = r; tempCol.G = g; tempCol.B = b;
            col.push_back(tempCol);
        }
        fclose(fp);
    }
}

void winrt::App1::implementation::MainWindow::MenuFlyoutItem_Click_3(winrt::Windows::Foundation::IInspectable const&, winrt::Microsoft::UI::Xaml::RoutedEventArgs const&)
{
    this->Close();
}
```

#### h 코드
```h
#include <winrt/Microsoft.Graphics.Canvas.UI.Xaml.h>
#include <winrt/Microsoft.UI.Input.h>  // for point_click
#include <winrt/Microsoft.UI.Xaml.Input.h> // for canvs point_click

using namespace winrt::Microsoft::UI;
using namespace winrt::Microsoft::Graphics::Canvas::UI::Xaml;
```
MainWindow
```h
bool flag = false;
int px = 0, py = 0, mySize = 8;

std::vector<int> vx;
std::vector<int> vy;
std::vector<struct winrt::Windows::UI::Color> col;
std::vector<int> size;
Windows::UI::Color myCol = Microsoft::UI::Colors::Green();
```
---
