---
layout: postlayout
title: "[转] MFC中ListControl控件的使用"
date:   2015-05-31 00:18:23 
categories: [MFC]
tags: [MFC, ListControl]
---

以下未经说明，listctrl默认view 风格为report

##1. CListCtrl 风格
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LVS_ICON: 为每个item显示大图标<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LVS_SMALLICON: 为每个item显示小图标<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LVS_LIST: 显示一列带有小图标的item<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LVS_REPORT: 显示item详细资料
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 直观的理解：windows资源管理器，&ldquo;查看&rdquo;标签下的&ldquo;大图标，小图标，列表，详细资料&rdquo;
&nbsp;

##2. 设置listctrl 风格及扩展风格
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LONG lStyle;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lStyle = GetWindowLong(m_list.m_hWnd, GWL_STYLE);//获取当前窗口style<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lStyle &amp;= ~LVS_TYPEMASK; //清除显示方式位<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lStyle |= LVS_REPORT; //设置style<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; SetWindowLong(m_list.m_hWnd, GWL_STYLE, lStyle);//设置style<br />&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; DWORD dwStyle = m_list.GetExtendedStyle();<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dwStyle |= LVS_EX_FULLROWSELECT;//选中某行使整行高亮（只适用与report风格的listctrl）<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dwStyle |= LVS_EX_GRIDLINES;//网格线（只适用与report风格的listctrl）<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dwStyle |= LVS_EX_CHECKBOXES;//item前生成checkbox控件<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; m_list.SetExtendedStyle(dwStyle); //设置扩展风格<br />&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 注：listview的style请查阅msdn<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="http://msdn.microsoft.com/library/default.asp?url=/library/en-us/wceshellui5/html/wce50lrflistviewstyles.asp">http://msdn.microsoft.com/library/default.asp?url=/library/en-us/wceshellui5/html/wce50lrflistviewstyles.asp

##3. 插入数据
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; m_list.InsertColumn( 0, "ID", LVCFMT_LEFT, 40 );//插入列<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; m_list.InsertColumn( 1, "NAME", LVCFMT_LEFT, 50 );<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; int nRow = m_list.InsertItem(0, &ldquo;11&rdquo;);//插入行<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; m_list.SetItemText(nRow, 1, &ldquo;jacky&rdquo;);//设置数据

##4. 一直选中item

&nbsp;&nbsp;&nbsp;&nbsp;选中style中的Show selection always，或者在上面第2点中设置LVS_SHOWSELALWAYS<br /><br /><br />

##5. 选中和取消选中一行
&nbsp;&nbsp;&nbsp; int nIndex = 0;<br />&nbsp;&nbsp;&nbsp; //选中<br />&nbsp;&nbsp;&nbsp; m_list.SetItemState(nIndex, LVIS_SELECTED|LVIS_FOCUSED, LVIS_SELECTED|LVIS_FOCUSED);<br />&nbsp;&nbsp;&nbsp; //取消选中<br />&nbsp;&nbsp;&nbsp; m_list.SetItemState(nIndex, 0, LVIS_SELECTED|LVIS_FOCUSED);

##6. 得到listctrl中所有行的checkbox的状态
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; m_list.SetExtendedStyle(LVS_EX_CHECKBOXES);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CString str;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; for(int i=0; i&lt;m_list.GetItemCount(); i++)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if( m_list.GetItemState(i, LVIS_SELECTED) == LVIS_SELECTED || m_list.GetCheck(i))<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; str.Format(_T("第%d行的checkbox为选中状态"), i);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AfxMessageBox(str);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }
&nbsp;

##7. 得到listctrl中所有选中行的序号
<h3><a name="t7">
<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 方法一：<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CString str;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; for(int i=0; i&lt;m_list.GetItemCount(); i++)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if( m_list.GetItemState(i, LVIS_SELECTED) == LVIS_SELECTED )<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; str.Format(_T("选中了第%d行"), i);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AfxMessageBox(str);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 方法二：<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; POSITION pos = m_list.GetFirstSelectedItemPosition();<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if (pos == NULL)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; TRACE0("No items were selected!/n");<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; else<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; while (pos)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; int nItem = m_list.GetNextSelectedItem(pos);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; TRACE1("Item %d was selected!/n", nItem);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // you could do your own processing on nItem here<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }
&nbsp;
##8. 得到item的信息
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; TCHAR szBuf[1024];<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LVITEM lvi;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvi.iItem = nItemIndex;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvi.iSubItem = 0;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvi.mask = LVIF_TEXT;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvi.pszText = szBuf;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvi.cchTextMax = 1024;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; m_list.GetItem(&amp;lvi);
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 关于得到设置item的状态，还可以参考msdn文章<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Q173242: Use Masks to Set/Get Item States in CListCtrl<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<a href="http://support.microsoft.com/kb/173242/en-us">http://support.microsoft.com/kb/173242/en-us
&nbsp;

##9. 得到listctrl的所有列的header字符串内容
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LVCOLUMN lvcol;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; char&nbsp; str[256];<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; int&nbsp;&nbsp; nColNum;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CString&nbsp; strColumnName[4];//假如有4列
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nColNum = 0;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvcol.mask = LVCF_TEXT;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvcol.pszText = str;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvcol.cchTextMax = 256;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; while(m_list.GetColumn(nColNum, &amp;lvcol))<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; strColumnName[nColNum] = lvcol.pszText;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nColNum++;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }
&nbsp;

##10. 使listctrl中一项可见，即滚动滚动条

&nbsp;&nbsp;&nbsp; m_list.EnsureVisible(i, FALSE);<br /><br />

##11. 得到listctrl列数

&nbsp;&nbsp;&nbsp; int nHeadNum = m_list.GetHeaderCtrl()-&gt;GetItemCount();<br /><br />

##12. 删除所有列
&nbsp;&nbsp;&nbsp; &nbsp; 方法一：<br />&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; while ( m_list.DeleteColumn (0))<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; 因为你删除了第一列后，后面的列会依次向上移动。
&nbsp;&nbsp;&nbsp; &nbsp; 方法二：<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; int nColumns = 4;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; for (int i=nColumns-1; i&gt;=0; i--)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; m_list.DeleteColumn (i);
&nbsp;

##13. 得到单击的listctrl的行列号
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 添加listctrl控件的NM_CLICK消息相应函数<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; void CTest6Dlg::OnClickList1(NMHDR* pNMHDR, LRESULT* pResult)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 方法一：<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; /*<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; DWORD dwPos = GetMessagePos();<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CPoint point( LOWORD(dwPos), HIWORD(dwPos) );<br />&nbsp;&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; m_list.ScreenToClient(&amp;point);<br />&nbsp;&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LVHITTESTINFO lvinfo;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvinfo.pt = point;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvinfo.flags = LVHT_ABOVE;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; int nItem = m_list.SubItemHitTest(&amp;lvinfo);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if(nItem != -1)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CString strtemp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; strtemp.Format("单击的是第%d行第%d列", lvinfo.iItem, lvinfo.iSubItem);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AfxMessageBox(strtemp);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; */<br />&nbsp;&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 方法二:<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; /*<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; NM_LISTVIEW* pNMListView = (NM_LISTVIEW*)pNMHDR;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if(pNMListView-&gt;iItem != -1)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CString strtemp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; strtemp.Format("单击的是第%d行第%d列",<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; pNMListView-&gt;iItem, pNMListView-&gt;iSubItem);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AfxMessageBox(strtemp);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; */<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *pResult = 0;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }

##14. 判断是否点击在listctrl的checkbox上
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 添加listctrl控件的NM_CLICK消息相应函数<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; void CTest6Dlg::OnClickList1(NMHDR* pNMHDR, LRESULT* pResult)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; DWORD dwPos = GetMessagePos();<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CPoint point( LOWORD(dwPos), HIWORD(dwPos) );<br />&nbsp;&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; m_list.ScreenToClient(&amp;point);<br />&nbsp;&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; LVHITTESTINFO lvinfo;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvinfo.pt = point;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; lvinfo.flags = LVHT_ABOVE;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; UINT nFlag;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; int nItem = m_list.HitTest(point, &amp;nFlag);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //判断是否点在checkbox上<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if(nFlag == LVHT_ONITEMSTATEICON)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; AfxMessageBox("点在listctrl的checkbox上");<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *pResult = 0;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }
&nbsp;

##15. 右键点击listctrl的item弹出菜单
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 添加listctrl控件的NM_RCLICK消息相应函数<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; void CTest6Dlg::OnRclickList1(NMHDR* pNMHDR, LRESULT* pResult)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; NM_LISTVIEW* pNMListView = (NM_LISTVIEW*)pNMHDR;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if(pNMListView-&gt;iItem != -1)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; DWORD dwPos = GetMessagePos();<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CPoint point( LOWORD(dwPos), HIWORD(dwPos) );<br />&nbsp;&nbsp;&nbsp;&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CMenu menu;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; VERIFY( menu.LoadMenu( IDR_MENU1 ) );<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; CMenu* popup = menu.GetSubMenu(0);<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ASSERT( popup != NULL );<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; popup-&gt;TrackPopupMenu(TPM_LEFTALIGN | TPM_RIGHTBUTTON, point.x, point.y, this );<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }&nbsp;<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *pResult = 0;<br />&nbsp; }&nbsp;

&nbsp;
&nbsp;
修改某一行的某一项
m_listRecvDetail.SetItem(m_listItemCount-2,3,LVIF_TEXT,"不应答",0,0,0,NULL);
Post Date: {{ page.date | date_to_string }}