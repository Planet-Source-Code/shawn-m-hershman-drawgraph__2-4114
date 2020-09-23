<div align="center">

## DrawGraph


</div>

### Description

Simply accepts integer x,y coordinates and graphs the points on a cartesian plane. The graph is immediately updated upon addition or deletion of a point.
 
### More Info
 
Was done pretty quickly while learning Java. The code probably isn't as smooth as I would like, but hey... The integer-only text boxes are pretty nice, though (even if I didn't write that part...) Please feel free to refine it and get back to me. It's all about learning to write efficient code, right??

None known.


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Shawn M\. Hershman](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/shawn-m-hershman.md)
**Level**          |Beginner
**User Rating**    |5.0 (20 globes from 4 users)
**Compatibility**  |Java \(JDK 1\.2\)
**Category**       |[Graphics](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/graphics__2-75.md)
**World**          |[Java](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/java.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/shawn-m-hershman-drawgraph__2-4114/archive/master.zip)





### Source Code

```
// DrawGraph
// by Shawn Hershman
//
// Allows you to add and delete integer x,y coordinate pairs and
// draws a graph of the points.
import java.util.*;
import java.lang.*;
import java.awt.*;
import java.awt.event.*;
import java.awt.geom.*;
import javax.swing.*;
import javax.swing.event.*;
import javax.swing.text.*;
// The intText... classes prevent the user from entering
// anything that isn't an integer
// This was borrowed from _CoreJava2, Vol 1_
//   by Cay Horstmann and Gary Cornell
class IntTextDocument extends PlainDocument {
  public void insertString(int offs, String str, AttributeSet a)
    throws BadLocationException {
    if (str == null) return;
    String oldString = getText(0, getLength());
    String newString = oldString.substring(0, offs)
              + str + oldString.substring(offs);
    try {
      // Check if new value with 0 concatenated is an integer
      Integer.parseInt(newString + "0");
      super.insertString(offs, str, a);
    }
    catch(NumberFormatException e) {}
  }
}
class IntTextField extends JTextField {
  public IntTextField(int size) {
    super(size);
  }
  public IntTextField(int defval, int size) {
    super("" + defval, size);
  }
  protected Document createDefaultModel() {
    return new IntTextDocument();
  }
  public boolean isValid() {
    try {
      Integer.parseInt(getText());
      return true;
    }
    catch(NumberFormatException e) {
      return false;
    }
  }
  public int getValue() {
    try {
      return Integer.parseInt(getText());
    }
    catch(NumberFormatException e) {
      return 0;
    }
  }
}
class DrawGraphPanel extends JPanel {
  private static final int BORDER_OFFSET = 20;
  private Vector sortedPoints = new Vector();
  private int maxY = 0;
  private int minY = 0;
  private int numPoints = 0;
  public DrawGraphPanel() {
  }
  public void paintComponent(Graphics g) {
    int xAxis;
    int yAxis;
    double xScaleFactor;
    double yScaleFactor;
    int xOffset;
    int yOffset;
    super.paintComponent(g);
    Dimension d = new Dimension();
    d = getSize();
    int panelHeight = (int)Math.round(d.getHeight());
    int panelWidth = (int)Math.round(d.getWidth());
    if (sortedPoints.size() == 1) {
      drawPoint(g, panelWidth/2, panelHeight/2, (int)((Point)sortedPoints.get(0)).getX(),
             (int)((Point)sortedPoints.get(0)).getY() );
      if (((Point)sortedPoints.get(0)).y > 0)
        yAxis = BORDER_OFFSET;
      else if (((Point)sortedPoints.get(0)).y < 0)
        yAxis = panelWidth - BORDER_OFFSET;
      else
        yAxis = panelWidth/2;
      if (((Point)sortedPoints.get(0)).x < 0)
        xAxis = BORDER_OFFSET;
      else if (((Point)sortedPoints.get(0)).x > 0)
        xAxis = panelHeight - BORDER_OFFSET;
      else
        xAxis = panelHeight/2;
      g.drawLine(BORDER_OFFSET, xAxis, panelWidth-BORDER_OFFSET, xAxis);
      g.drawLine(yAxis, BORDER_OFFSET, yAxis, panelHeight-BORDER_OFFSET);
    }
    else if (sortedPoints.size() > 1) {
      int xdiff = (int)((Point)sortedPoints.get(sortedPoints.size()-1)).getX() -
            (int)((Point)sortedPoints.get(0)).getX();
      xScaleFactor = (panelWidth-2*BORDER_OFFSET)/xdiff;
      int ydiff = maxY - minY;
      if (ydiff == 0) ydiff = 1;
      yScaleFactor = (panelHeight-2*BORDER_OFFSET)/ydiff;
      xOffset = (int)(((Point)sortedPoints.get(0)).getX() * xScaleFactor - BORDER_OFFSET);
      yOffset = (int)(minY * yScaleFactor - BORDER_OFFSET);
      // Draw the lines
      for (int i=1; i < sortedPoints.size(); i++) {
        g.drawLine((int)(((Point)sortedPoints.get(i-1)).getX() * xScaleFactor - xOffset),
             panelHeight - (int)(((Point)sortedPoints.get(i-1)).getY() * yScaleFactor - yOffset),
             (int)(((Point)sortedPoints.get(i)).getX() * xScaleFactor - xOffset),
             panelHeight - (int)(((Point)sortedPoints.get(i)).getY() * yScaleFactor - yOffset));
      }
      // Draw the data points
      for (int j=0; j < sortedPoints.size(); j++) {
        int x = (int)(((Point)sortedPoints.get(j)).getX() * xScaleFactor - xOffset);
        int y = panelHeight - (int)(((Point)sortedPoints.get(j)).getY() * yScaleFactor - yOffset);
        drawPoint(g, x, y, (int)(((Point)sortedPoints.get(j)).getX()),
               (int)(((Point)sortedPoints.get(j)).getY()) );
      }
      // Draw axes
      g.drawLine(BORDER_OFFSET, panelHeight+yOffset, panelWidth-BORDER_OFFSET, panelHeight+yOffset);
      g.drawLine(-xOffset, BORDER_OFFSET, -xOffset, panelHeight-BORDER_OFFSET);
    }
    sortedPoints.removeAllElements();
  }
  private void drawPoint(Graphics g, int x, int y, int xVal, int yVal) {
    g.fillRect(x-1, y-1, 3, 3);
    g.drawRect(x-3, y-3, 6, 6);
    g.drawString( "(" + xVal + "," + yVal + ")", x-15, y+15);
  }
  public void setPoints(Vector rcvdVector) {
    int index;
    int value;
    Vector v = (Vector)rcvdVector.clone();
    sortedPoints.removeAllElements();
    numPoints = v.size();
    if (numPoints > 0) {
      minY = maxY = (int)((Point)v.get(0)).getY();
    }
    while (v.size()>0) {
      value = (int)((Point)v.get(0)).getX();
      index = 0;
      for (int i=1; i<v.size(); i++) {
        if ((int)((Point)v.get(i)).getX() < value) {
          value = (int)((Point)v.get(i)).getX();
          index = i;
        }
      }
      sortedPoints.addElement(v.get(index));
      if ((int)((Point)v.get(index)).getY() < minY) {
        minY = (int)((Point)v.get(index)).getY();
      }
      if ((int)((Point)v.get(index)).getY() > maxY) {
        maxY = (int)((Point)v.get(index)).getY();
      }
      v.remove(index);
    }
  }
}
class DrawGraphFrame extends JFrame
  implements ActionListener, ListSelectionListener {
  private DrawGraphPanel graphPanel = new DrawGraphPanel();
  private JPanel controlPanel = new JPanel();
  private Vector listStrings = new Vector();
  private Vector listPoints = new Vector();
  private String removeString = new String();
  private JList pointList;
  private JScrollPane scrollPane;
  private JButton deletePointButton;
  private JButton clearPointsButton;
  private JButton addPointButton;
  private IntTextField xValue;
  private IntTextField yValue;
  public DrawGraphFrame() {
    setTitle("Draw Graph");
    setSize(750, 500);
    setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    addWindowListener( new WindowAdapter() {
      public void windowClosing() {
        System.exit(0);
      }
    });
    pointList = new JList();
    pointList.setSelectionMode(ListSelectionModel.SINGLE_SELECTION);
    pointList.setFixedCellWidth(100);
    pointList.addListSelectionListener(this);
    scrollPane = new JScrollPane(pointList);
    deletePointButton = new JButton("Delete Point");
    deletePointButton.addActionListener(this);
    clearPointsButton = new JButton("Clear All");
    clearPointsButton.addActionListener(this);
    Box deleteButtons = Box.createHorizontalBox();
    deleteButtons.add(deletePointButton);
    deleteButtons.add(clearPointsButton);
    xValue = new IntTextField(3);
    yValue = new IntTextField(3);
    Box newPoint = Box.createHorizontalBox();
    newPoint.add(new JLabel("X:"));
    newPoint.add(xValue);
    newPoint.add(new JLabel("   Y:"));
    newPoint.add(yValue);
    addPointButton = new JButton("Add Point");
    addPointButton.addActionListener(this);
    Box b = Box.createVerticalBox();
    b.add(scrollPane);
    b.add(deleteButtons);
    b.add(new JLabel("   "));
    b.add(newPoint);
    b.add(addPointButton);
    controlPanel.add(b);
    getContentPane().add(graphPanel, "Center");
    getContentPane().add(controlPanel, "East");
    xValue.requestFocus();
    repaint();
  }
  public void valueChanged(ListSelectionEvent evt) {
    JList source = (JList)evt.getSource();
    if (source == pointList) {
      removeString = (String)pointList.getSelectedValue();
    }
  }
  public void actionPerformed(ActionEvent evt) {
    int index;
    Object s = evt.getSource();
    if (s == addPointButton) {
      Point p = new Point();
      if (xValue.getText().length() > 0 && yValue.getText().length() > 0 ) {
        p.setLocation(xValue.getValue(), yValue.getValue());
        if ( xValueExists(xValue.getValue()) == false ) {
          listStrings.add("(" + p.x + ", " + p.y + ")");
          listPoints.add(p);
          xValue.setText("");
          yValue.setText("");
          xValue.requestFocus();
        }
      }
    }
    else if (s == clearPointsButton) {
      listPoints.clear();
      listStrings.clear();
    }
    else if (s == deletePointButton) {
      if ((index = listStrings.indexOf(removeString)) > -1) {
        listPoints.remove(index);
        listStrings.remove(index);
      }
    }
    pointList.setListData(listStrings);
    graphPanel.setPoints(listPoints);
    repaint();
  }
  private boolean xValueExists(int x) {
    for (int i = 0; i < listPoints.size(); i++) {
      if (((Point)listPoints.get(i)).x == x)
        return true;
    }
    return false;
  }
}
public class DrawGraph {
  public static void main(String[] args) {
    JFrame frame = new DrawGraphFrame();
    frame.show();
  }
}
```

