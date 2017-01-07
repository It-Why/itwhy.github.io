***窗口大小改变后控件大小也随之改变***
#在对话框头文件中定义
头文件名称：项目名称Dlg.h

	POINT Old;//存放对话框的宽和高。
	
#对话框cpp中初始化
cpp文件名称：项目名称Dlg.cpp
在OnInitDialog()函数中添加

	CRect rect;
	GetClientRect(&rect); //取客户区大小   
	Old.x = rect.right - rect.left;
	Old.y = rect.bottom - rect.top;
	
#添加WM_SIZE消息
在类向导中为对话框类添加WM_SIZE消息，如图
![添加WM_SIZE消息](http://img.blog.csdn.net/20161230214852830?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2h5MTk5NDA5MjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
然后编辑代码，在函数中添加以下代码

	void C枪支编码识别Dlg::OnSize(UINT nType, int cx, int cy)
{
	CDialogEx::OnSize(nType, cx, cy);

	// TODO:  在此处添加消息处理程序代码
	if (nType == SIZE_RESTORED || nType == SIZE_MAXIMIZED)//窗体大小发生变动。处理程序
	{
		float fsp[2];
		POINT Newp; //获取现在对话框的大小
		CRect recta;
		GetClientRect(&recta); //取客户区大小   
		Newp.x = recta.right - recta.left;
		Newp.y = recta.bottom - recta.top;
		fsp[0] = (float)Newp.x / Old.x;
		fsp[1] = (float)Newp.y / Old.y;
		CRect Rect;
		int woc;
		CPoint OldTLPoint, TLPoint; //左上角
		CPoint OldBRPoint, BRPoint; //右下角
		HWND hwndChild = ::GetWindow(m_hWnd, GW_CHILD); //列出所有控件   
		while (hwndChild)
		{
			woc = ::GetDlgCtrlID(hwndChild);//取得ID
			GetDlgItem(woc)->GetWindowRect(Rect);
			ScreenToClient(Rect);
			OldTLPoint = Rect.TopLeft();
			TLPoint.x = long(OldTLPoint.x*fsp[0]);
			TLPoint.y = long(OldTLPoint.y*fsp[1]);
			OldBRPoint = Rect.BottomRight();
			BRPoint.x = long(OldBRPoint.x *fsp[0]);
			BRPoint.y = long(OldBRPoint.y *fsp[1]); //高度不可读的控件（如:combBox),不要改变此值.
			Rect.SetRect(TLPoint, BRPoint);
			GetDlgItem(woc)->MoveWindow(Rect, TRUE);
			hwndChild = ::GetWindow(hwndChild, GW_HWNDNEXT);
		}
		Old = Newp;
	}
}
