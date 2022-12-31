# J Button 과 J Text Field 에 기능 추가하기

> # J Button

## 1.action lister method
    

```java
JButton.addActionListener(new ActionListener() {
				// action listener 사용시 필수로 따라오는 method
		@Override
		public void actionPerformed(ActionEvent e) {
			// 이곳에 원하는 기능을 작성 하면됨
		}});
```

<br>

## 2.버튼 클릭시 원하는 text 가 field 또는 area 에 타이핑 되는 기능

```java
JButton.addActionListener(new ActionListener() {
				
		@Override
		public void actionPerformed(ActionEvent e) {
			TextArea.append("Show this Text! \n ");
		}});
```

<br>

## 3.실행중인 모든 window를 종료하는 기능

```java
JButton.addActionListener(new ActionListener() {
				
		@Override
		public void actionPerformed(ActionEvent e) {
			System.exit(0);
		}});
```

<br>

## 4.Label의 text를 Field 에 입력한 text 로 변경하는 기능

```java
JButton.addActionListener(new ActionListener() {
				
		@Override
		public void actionPerformed(ActionEvent e) {
		  Label.setText(TextField.getText());
		}});
```
<br>

> # J Text Field

## 1. KeyListener Method

```java
JTextField.addKeyListener(new KeyAdapter() {
		public void keyReleased(KeyEvent e) {
			  // 원하는 기능 작성하는 곳
		}
	});
```
<br>

## 2. text 입력으로 table 의 원하는 값 search 하기

```java
JTextField.addKeyListener(new KeyAdapter() {
		public void keyReleased(KeyEvent e) {
          // text 입력값을 변수로 만들어줌
			String value = JTextField.getText();
          // table row sorter Class 의 instance 생성 (Parameter 값으로 table 의 model값을 넣어줌
			TableRowSorter<TableModel> trs = new TableRowSorter<>(table.getModel());
          // 만들어놓은 정리 기능을 table 에 넣어줌
			table.setRowSorter(trs);
          // row 를 정리하는 매소드
			trs.setRowFilter(RowFilter.regexFilter(value));
		}
	});
```

## 전체 code

```java
import java.awt.BorderLayout;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.*;

public class TodoFrame {
	
	public static void frame () {
		JFrame fr =new JFrame();
				// 레이아웃을 나누기위한 메인 패
		JPanel pnl = new JPanel ();
				// 페널을 한번 더 나눠서 버튼만 담는 용도의 패널 
		JPanel pnl_btn = new JPanel ();
		JTextArea tr = new JTextArea ();
		JButton btn1 = new JButton ("Wow");
		JButton btn2 = new JButton ("Exit");
		JButton btn3 = new JButton ("Change");
		JLabel la = new JLabel ("Wellcome !");
		
		
		pnl.setLayout(new BorderLayout());
				// 버튼 패널을 메인패널의 서쪽으로 위치 
		pnl.add(pnl_btn, BorderLayout.WEST);
		pnl.add(tr, BorderLayout.CENTER);
		pnl.add(la, BorderLayout.NORTH);
				// 버튼 패널 배경색 변경 
		pnl_btn.setBackground(Color.LIGHT_GRAY);
		
				// 버튼1 의 작동을 위한 액션 method 생
		btn1.addActionListener(new ActionListener() {
				// 액션 리스너 사용시 필수로 따라오는 method
			@Override
			public void actionPerformed(ActionEvent e) {
					//버튼1 클릭할경우 text area에 나타내는 기능 
				tr.append("Oh My got-!  Wow-!\n ");
			}
		});
		btn2.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
					//모든 프로그램을 종료하는 기능 
				System.exit(0);
			}
		});
		btn3.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
					// label의 텍스트 변경 (text area의 text로)
				la.setText(tr.getText());
			}
		});
		
		pnl_btn.add(btn1);
		pnl_btn.add(btn2);
		pnl_btn.add(btn3);
		fr.add(pnl);
		
		
		fr.setResizable(false);
		fr.setVisible(true);	
		fr.setPreferredSize(new Dimension(840, 840/12*9));
		fr.setSize(840, 840/12*9);
		fr.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		fr.setLocationRelativeTo(null);
	}
}

```

[참고](https://youtu.be/YJJwKCf2uOA) - Danny TWLC : 자바 스윙3
[J Frame 만드는법](https://choideakook.github.io/jfram/)
